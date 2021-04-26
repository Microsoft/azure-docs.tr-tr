---
title: İşleme için depolama ve veri taşıma
description: Varlık ve çıkış dosyası iş yüklerini işlemek için çeşitli depolama ve veri taşıma seçenekleri hakkında bilgi edinin.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 0a18ee6961cb601b0fa9db7213eb6115afa20096
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765206"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Varlık ve çıkış dosyalarını işlemeye yönelik depolama ve veri taşıma seçenekleri

Sahne ve varlık dosyalarını havuz VM 'lerinde işleme uygulamaları için kullanılabilir hale getirmek için birden çok seçenek vardır:

* [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md):
  * Sahne ve varlık dosyaları yerel bir dosya sisteminden BLOB depolama alanına yüklenir. Uygulama bir görev tarafından çalıştırıldığında, gereken dosyalar, işleme uygulaması tarafından erişilebilmesi için blob depolamadan sanal makineye kopyalanır. Çıktı dosyaları, işleme uygulaması tarafından VM diskine yazılır ve sonra blob depolamaya kopyalanır.  Gerekirse, çıkış dosyaları blob depolamadan yerel bir dosya sistemine indirilebilir.
  * Azure Blob depolama, daha küçük projeler için basit ve ekonomik bir seçenektir.  Her havuz sanal makinesinde tüm varlık dosyaları gerektiğinden, dosya aktarımlarının mümkün olduğunca verimli olmasını sağlamak için varlık dosyalarının sayısı ve boyutu artar.  
* [Blobsigortası](../storage/blobs/storage-how-to-mount-container-linux.md)kullanan bir dosya sistemi olarak Azure depolama:
  * Linux VM 'Ler için, blobsigortası sanal dosya sistemi sürücüsü kullanıldığında bir depolama hesabı gösterilebilir ve dosya sistemi olarak kullanılabilir.
  * Bu seçenek, dosya sistemi için sanal makine gerekmediği ve VM 'lerde blobsigortası önbelleğe alma işlemleri, birden fazla iş ve görev için aynı dosyaların tekrarlanmış indirmesini önlediği için çok uygun maliyetli bir avantajdır.  Dosyalar yalnızca blob 'lar ve standart API 'Ler ve AzCopy gibi araçlar ve bir şirket içi dosya sistemi ile Azure depolama arasında dosya kopyalamak için kullanılabilir.
* Dosya sistemi veya dosya paylaşma:
  * VM işletim sistemi ve performans/ölçek gereksinimlerine bağlı olarak, Azure dosyaları, örneğin, GlusterFS gibi dağıtılmış bir dosya sistemi için bağlı disklere sahip birden fazla VM kullanarak veya bir üçüncü taraf teklifi kullanarak [Azure dosyalarını](../storage/files/storage-files-introduction.md), NFS için bağlı disklere sahıp bir VM 'yi kullanarak içerir.
  * [Avere sistemleri](https://www.averesystems.com/) artık Microsoft 'un bir parçasıdır ve yakın gelecekte büyük ölçekli ve yüksek performanslı işleme için ideal çözümler sunulacaktır.  Avere çözümü, BLOB depolama ile veya şirket içi NAS cihazlarıyla birlikte çalışan bir Azure tabanlı NFS veya SMB önbelleğinin oluşturulmasını sağlayacaktır.
  * Dosya sistemi ile, dosyalar doğrudan dosya sistemine okunabilir veya yazılabilir veya dosya sistemi ile havuz VM 'Leri arasında kopyalanabilir.
  * Paylaşılan bir dosya sistemi, proje ve işler arasında paylaşılan çok sayıda varlık olmasına olanak sağlar. bu sayede, işleme görevleri yalnızca gerekli olan erişime sahiptir.

## <a name="using-azure-blob-storage"></a>Azure Blob depolamayı kullanma

BLOB depolama hesabı veya genel amaçlı v2 depolama hesabı kullanılmalıdır.  Bu iki depolama hesabı türü, genel amaçlı bir v1 depolama hesabıyla karşılaştırıldığında, [Bu blog gönderisine](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)göre önemli ölçüde daha yüksek sınırlara göre yapılandırılabilir.  Yapılandırıldığında, özellikle depolama hesabına erişen çok sayıda havuz VM olduğunda, daha yüksek olan sınırlar çok daha iyi performans ve ölçeklenebilirlik sağlayacaktır.

### <a name="copying-files-between-client-and-blob-storage"></a>İstemci ve BLOB depolama arasında dosya kopyalama

Azure depolama 'ya ve Azure Storage 'a dosya kopyalamak için, Depolama Blobu API 'SI, [Azure depolama veri taşıma kitaplığı](https://github.com/Azure/azure-storage-net-data-movement), [Windows](../storage/common/storage-use-azcopy-v10.md) veya [Linux](../storage/common/storage-use-azcopy-v10.md)için azcopy komut satırı aracı, [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)ve [Azure Batch Explorer](https://azure.github.io/BatchExplorer/)dahil çeşitli mekanizmalar kullanılabilir.

Örneğin, AzCopy kullanarak bir klasördeki tüm varlıklar şu şekilde aktarılabilir:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Yalnızca değiştirilen dosyaları kopyalamak için,/XO parametresi kullanılabilir:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Giriş varlık dosyalarını blob depolamadan Batch havuzu VM 'lerine kopyalama

Dosyaları, iş varlıklarının boyutuyla belirlenen en iyi yaklaşımla kopyalamak için birkaç farklı yaklaşım vardır.
En basit yaklaşım, tüm varlık dosyalarını her iş için havuz VM 'lerine kopyalamadır:

* Bir işe özgü dosyalar olduğunda ancak bir işin tüm görevleri için gerekliyse, tüm dosyaları kopyalamak için bir [iş hazırlama görevi](/rest/api/batchservice/job/add#jobpreparationtask) belirlenebilir.  İş hazırlama görevi, bir VM 'de ilk iş görevi yürütüldüğünde bir kez çalıştırılır, ancak sonraki iş görevleri için yeniden çalıştırılmaz.
* İş tamamlandıktan sonra iş başına dosyaları kaldırmak için bir [iş bırakma görevi](/rest/api/batchservice/job/add#jobreleasetask) belirtilmelidir; Bu, VM diskinin tüm iş varlık dosyaları tarafından doldurulmasını önler.
* Aynı varlıkları kullanan birden çok iş olduğunda, her iş için yalnızca varlıklarda yapılan artımlı değişiklikler olduğunda, yalnızca bir alt küme güncelleştirilmiş olsa bile tüm varlık dosyaları yine de kopyalanır.  Çok sayıda büyük varlık dosyası olduğunda bu verimsiz olur.

Varlıklar arasında varlık dosyaları yeniden kullanıldığında, işler arasında yalnızca artımlı değişiklikler olduğunda daha verimli ancak daha fazla ilgili yaklaşım, varlıkları sanal makinede paylaşılan klasöre depolamak ve değiştirilen dosyaları eşitlemek için kullanılır.

* İş hazırlama görevi, AZ_BATCH_NODE_SHARED_DIR ortam değişkeni tarafından belirtilen VM paylaşılan klasörü için/XO parametresiyle AzCopy kullanarak kopyayı gerçekleştirir.  Bu, yalnızca değiştirilen dosyaları her VM 'ye kopyalar.
* Bu kaynakların havuz VM 'lerinin geçici sürücüsüne sığması için tüm varlıkların boyutuna verilmesi gerekir.

Azure Batch, dosyaları bir depolama hesabı ve toplu Iş havuzu VM 'Leri arasında kopyalamak için yerleşik desteğe sahiptir.  Görev [kaynak dosyaları](/rest/api/batchservice/job/add#resourcefile) depolama alanından havuz VM 'lerine dosya kopyalar ve iş hazırlama görevi için belirtilebilir.  Ne yazık ki yüzlerce dosya olduğunda, başarısız olması için bir sınır ve görev olması mümkündür.  Çok sayıda varlık olduğunda, iş hazırlama görevinde AzCopy komut satırını kullanmanız önerilir ve bu da joker karakter kullanabilir ve sınır yoktur.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Çıktı dosyalarını, Batch havuzu VM 'lerinden blob depolamaya kopyalama

[Çıktı dosyaları](/rest/api/batchservice/task/add#outputfile) , BIR havuz VM 'sinden depolamaya dosya kopyalamak için kullanılabilir.  Görev tamamlandıktan sonra bir veya daha fazla dosya VM 'den belirtilen depolama hesabına kopyalanabilir.  İşlenen çıktının kopyalanması gerekir, ancak günlük dosyalarını depolamak da istenebilir.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Linux VM havuzları için blobsigortası sanal dosya sistemi kullanma

Blobsigortası, Azure Blob depolama için bir sanal dosya sistemi sürücüsüdür ve bu sayede, Linux dosya sistemi aracılığıyla bir depolama hesabında blob olarak depolanan dosyalara erişebilirsiniz.

Havuz düğümleri, başlatıldığında dosya sistemini bağlayabilir veya bir iş hazırlama görevinin bir parçası olarak bağlama gerçekleşebilir. bir görev, yalnızca bir iş içindeki ilk görev bir düğümde çalıştığında çalışır.  Blobsigortası, dosyaları önbelleğe almak için hem Ramdisk hem de VM 'Lerin yerel SSD 'sini kullanacak şekilde yapılandırılabilir. Bu, bir düğümdeki birden çok görev aynı dosyalardan bazılarına erişebilse performansı önemli ölçüde artırır.

[Örnek şablonlar,](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) bir blobsigortası dosya sistemi kullanan tek başına V-Ray işlemeyi çalıştırmak için kullanılabilir ve diğer uygulamaların şablonlarının temeli olarak kullanılabilir.

### <a name="accessing-files"></a>Dosyalara erişme

İş görevleri bağlı dosya sistemini kullanarak giriş dosyaları ve çıkış dosyaları için yollar belirtir.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Giriş varlık dosyalarını blob depolamadan Batch havuzu VM 'lerine kopyalama

Dosyalar yalnızca Azure Storage 'daki blob 'lar olduğundan, standart blob API 'Leri, Araçlar ve UIS 'Ler, bir şirket içi dosya sistemi ve BLOB depolama arasında dosya kopyalamak için kullanılabilir; Örneğin, AzCopy, Depolama Gezgini, Batch Explorer vb.

## <a name="using-azure-files-with-windows-vms"></a>Windows VM 'leriyle Azure dosyalarını kullanma

[Azure dosyaları](../storage/files/storage-files-introduction.md) , bulutta SMB protokolü aracılığıyla erişilebilen tam olarak yönetilen dosya paylaşımları sunar.  Azure dosyaları, Azure Blob depolama alanını temel alır; Bu, uygun [maliyetli](https://azure.microsoft.com/pricing/details/storage/files/) ve genel olarak yedekli şekilde başka bir bölgeye veri çoğaltma ile yapılandırılabilir.  [Ölçek hedefleri](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) , Azure dosyalarının tahmin havuzu boyutu ve varlık dosyalarının sayısı verildiğinde kullanılması gerekip gerekmediğini öğrenmek için incelenmelidir.

Azure dosya paylaşımının nasıl bağlanacağını kapsayan [Belgeler](../storage/files/storage-how-to-use-files-windows.md) vardır.

### <a name="mounting-an-azure-files-share"></a>Azure dosya paylaşma bağlama

Batch 'de kullanmak için, görevler arasında bağlantıyı sürdürmek mümkün olmadığı için bir görev her çalıştırıldığında bir bağlama işleminin gerçekleştirilmesi gerekir.  Bunu yapmanın en kolay yolu, havuz yapılandırmasındaki başlangıç görevini kullanarak kimlik bilgilerini kalıcı hale getirmek için cmdkey, sonra da her görevden önce paylaşımdan bağlantı oluşturmak için kullanın.

Bir havuz şablonunda cmdkey 'ın örnek kullanımı (JSON dosyasında kullanım için kaçan) – başlangıç görevinin Kullanıcı bağlamının, görevleri çalıştırmak için kullanılan ile aynı olması gerektiğini unutmayın:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Örnek iş görevi komut satırı:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Dosyalara erişme

İş görevleri bağlı dosya sistemini kullanarak, eşlenmiş bir sürücü veya UNC yolu kullanarak giriş dosyaları ve çıkış dosyaları için yollar belirtir.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Giriş varlık dosyalarını blob depolamadan Batch havuzu VM 'lerine kopyalama

Azure dosyaları, Azure depolama desteği olan tüm ana API 'Ler ve araçlar tarafından desteklenir; Örneğin, AzCopy, Azure CLı, Depolama Gezgini, Azure PowerShell, Batch Explorer vb.

[Azure dosya eşitleme](../storage/file-sync/file-sync-planning.md) , bir şirket içi dosya sistemi ve bir Azure dosya paylaşımından dosyaları otomatik olarak eşitlemeye yönelik olarak kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Depolama seçenekleri hakkında daha fazla bilgi için bkz. ayrıntılı belgeler:

* [Azure blob depolama](../storage/blobs/storage-blobs-introduction.md)
* [Blobsigortası](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Azure Dosyaları](../storage/files/storage-files-introduction.md)
