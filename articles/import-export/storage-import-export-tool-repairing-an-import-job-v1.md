---
title: Azure Içeri/dışarı aktarma içeri aktarma işi onarılıyor-v1 | Microsoft Docs
description: Azure Içeri/dışarı aktarma hizmeti kullanılarak oluşturulan ve çalıştırılan bir içeri aktarma işini onarmayı öğrenin.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: b0eeb73ffb5436284f4f23cef943db0a3307a2b2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564577"
---
# <a name="repairing-an-import-job"></a>Bir içeri aktarma işini onarma
Microsoft Azure İçeri/Dışarı Aktarma hizmeti, bazı dosyalarınızı veya bir dosyanın bazı bölümlerini Windows Azure Blob hizmetine kopyalayamayabilir. Hatalar şunlardan kaynaklanabilir:  
  
-   Bozuk dosyalar  
  
-   Hasarlı sürücüler  
  
-   Dosya aktarılırken depolama hesabı anahtarı değiştirildi.  
  
Microsoft Azure İçeri/Dışarı Aktarma aracını içeri aktarma işinin kopyalama günlük dosyalarıyla çalıştırabilirsiniz. Araç, içeri aktarma işini tamamlaması için eksik dosyaları veya bir dosyanın parçalarını Windows Azure Storage hesabınıza yükler.  
  
## <a name="repairimport-parameters"></a>Repairımport parametreleri

Aşağıdaki parametreler **Repairımport** ile belirtilebilir: 
  
| Parametre | Açıklama |  
|-|-|  
|**/r:**<repairFile\>|**Gerekli.** Onarımın ilerlemesini izleyen ve kesilen bir onarımı sürdürmeye devam etmenizi sağlayan onarım dosyasının yolu. Her sürücünün bir ve yalnızca bir onarım dosyası olmalıdır. Belirli bir sürücü için onarım başlattığınızda, yolu henüz mevcut olmayan bir onarım dosyasına geçirin. Kesilen bir onarımın sürdürülmesi için, var olan bir onarım dosyasının adını geçirmeniz gerekir. Her zaman hedef sürücüye karşılık gelen onarım dosyasını belirtin.|  
|**/logdir:**<LogDirectory\>|**Seçim.** Günlük dizini. Ayrıntılı günlük dosyaları bu dizine yazılır. Günlük dizini belirtilmemişse, geçerli dizin günlük dizini olarak kullanılır.|  
|**/d:**<targetdizinler\>|**Gerekli.** İçeri aktarılan özgün dosyaları içeren bir veya daha fazla noktalı virgülle ayrılmış dizin. İçeri aktarma sürücüsü de kullanılabilir, ancak özgün dosyaların alternatif konumları varsa gerekli değildir.|  
|**/BK:**<BitLockerKey\>|**Seçim.** Aracın, özgün dosyaların kullanılabildiği şifreli bir sürücünün kilidini açmak istiyorsanız BitLocker anahtarını belirtin.|  
|**/sn:**<storageAccountName\>|**Gerekli.** İçeri aktarma işinin depolama hesabının adı.|  
|**/SK:**<StorageAccountKey\>|Yalnızca bir kapsayıcı SAS belirtilmemişse **gereklidir** . İçeri aktarma işi için depolama hesabının hesap anahtarı.|  
|**/csas:**<containersas\>|Yalnızca depolama hesabı anahtarı belirtilmemişse **gereklidir** . İçeri aktarma işiyle ilişkili bloblara erişmek için kapsayıcı SAS.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Gerekli.** Sürücü kopyalama günlük dosyasının yolu (ayrıntılı günlük veya hata günlüğü). Dosya, Windows Azure Içeri/dışarı aktarma hizmeti tarafından oluşturulur ve işle ilişkili blob depolamadan indirilebilir. Kopyalama günlük dosyası, başarısız Bloblar veya dosyalar hakkında onarılabilecek bilgiler içerir.|  
|**/Pathmapfile:**<DrivePathMapFile\>|**Seçim.** Aynı işte içeri aktardığınız aynı ada sahip birden çok dosyanız varsa, belirsizlikleri çözümlemek için kullanılan bir metin dosyasının yolu. Araç ilk kez çalıştırıldığında, bu dosyayı tüm belirsiz adlarla doldurabilir. Daha sonra aracın çalıştırmaları, belirsizlikleri çözümlemek için bu dosyayı kullanır.|  
  
## <a name="using-the-repairimport-command"></a>Repairımport komutunu kullanma  
Verileri ağ üzerinden akışa alarak içeri aktarma verilerini onarmak için, parametresini kullanarak içe aktardığınız özgün dosyaları içeren dizinleri belirtmeniz gerekir `/d` . Depolama hesabınızdan indirdiğiniz kopyalama günlük dosyasını da belirtin. Kısmi hatalarla bir içeri aktarma işini onarmak için tipik bir komut satırı şöyle görünür:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
Aşağıdaki bir kopya günlük dosyası örneğinde, bir dosyanın 1 64-K parçası içeri aktarma işi için gönderilen sürücüdeki bozulmuş. Bu hata yalnızca belirtilen tek bir sorun olduğundan, işteki Blobların geri kalanı başarıyla içeri aktarıldı.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Bu kopyalama günlüğü Azure Içeri/dışarı aktarma aracına geçirildiğinde, araç eksik içeriği ağ üzerinden kopyalayarak bu dosyanın içeri aktarılmasını tamamlamayı dener. Yukarıdaki örnekte yer alan araç, bu `\animals\koala.jpg` iki dizin ve içindeki orijinal dosyayı arar `C:\Users\bob\Pictures` `X:\BobBackup\photos` . Dosya `C:\Users\bob\Pictures\animals\koala.jpg` varsa, Azure içeri/dışarı aktarma aracı eksik veri aralığını karşılık gelen bloba kopyalar `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg` .  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Repairımport kullanılırken çakışmalar çözümleniyor  
Bazı durumlarda, araç aşağıdaki nedenlerden biri için gerekli dosyayı bulamıyor veya açamıyor olabilir: dosya bulunamadı, dosya erişilebilir durumda değil, dosya adı belirsiz veya dosyanın içeriği artık doğru değil.  
  
Araç, `\animals\koala.jpg` ve içinde bu ada sahip bir dosya bulmaya çalışıyorsa belirsiz bir hata oluşabilir `C:\Users\bob\pictures` `X:\BobBackup\photos` . Diğer bir deyişle, `C:\Users\bob\pictures\animals\koala.jpg` `X:\BobBackup\photos\animals\koala.jpg` içeri aktarma işi sürücülerinde her ikisi de mevcuttur.  
  
`/PathMapFile`Seçeneği bu hataları çözmenize olanak sağlar. Aracın doğru bir şekilde belirleyemedik dosyaların listesini içeren dosyanın adını belirtebilirsiniz. Aşağıdaki komut satırı örneği doldurulur `9WM35C2V_pathmap.txt` :  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Araç daha sonra sorunlu dosya yollarını `9WM35C2V_pathmap.txt` her satıra bir olacak şekilde yazar. Örneğin, dosyası komutunu çalıştırdıktan sonra dosya aşağıdaki girişleri içerebilir:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Listedeki her dosya için, aracın kullanılabilir olduğundan emin olmak için dosyayı bulmayı ve açmayı denemeniz gerekir. Araca bir dosyayı nerede bulacağınızı açıkça söylemek istiyorsanız, yol haritası dosyasını değiştirin ve yolu bir sekme karakteriyle ayırarak aynı satırdaki her bir dosyaya ekleyin:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Gerekli dosyaları araç için kullanılabilir hale getirdikten veya yol haritası dosyasını güncelleştirdikten sonra, içeri aktarma işlemini gerçekleştirmek için aracı yeniden çalıştırabilirsiniz.  
  
## <a name="next-steps"></a>Sonraki adımlar
 
* [Azure Içeri/dışarı aktarma aracı 'nı ayarlama](storage-import-export-tool-setup-v1.md)   
* [Sabit sürücüleri içeri aktarma işine hazırlama](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
* [Kopyalama günlük dosyalarıyla iş durumunu gözden geçirme](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Bir dışarı aktarma işini onarma](./storage-import-export-tool-repairing-an-export-job-v1.md)