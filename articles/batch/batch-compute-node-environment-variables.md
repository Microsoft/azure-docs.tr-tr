---
title: Görev çalışma zamanı ortam değişkenleri
description: Azure Batch Analytics için görev çalışma zamanı ortamı değişken Kılavuzu ve başvurusu.
ms.topic: conceptual
ms.date: 12/30/2020
ms.openlocfilehash: dbdc13e28a3a0c772480d2602f147e0d3354ff48
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669993"
---
# <a name="azure-batch-runtime-environment-variables"></a>Çalışma zamanı ortam değişkenlerini Azure Batch

[Azure Batch hizmeti](https://azure.microsoft.com/services/batch/) , işlem düğümlerinde aşağıdaki ortam değişkenlerini ayarlar. Görev komut satırlarında bu ortam değişkenlerine ve komut satırları tarafından çalıştırılan programlar ve betiklere başvurabilirsiniz.

Batch ile ortam değişkenlerini kullanma hakkında daha fazla bilgi için bkz. [Görevler Için ortam ayarları](jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Ortam değişkeni görünürlüğü

Bu ortam değişkenleri, yalnızca görev **kullanıcısı** bağlamında görünür, bu, bir görevin yürütüldüğü düğümdeki kullanıcı hesabıdır. Uzak Masaüstü Protokolü (RDP) veya Secure Shell (SSH) aracılığıyla bir işlem düğümüne [Uzaktan bağlanırken](./error-handling.md#connect-to-compute-nodes) bu değişkenleri görmezsiniz ve ortam değişkenlerini listeleme. Bunun nedeni, uzak bağlantı için kullanılan kullanıcı hesabının görev tarafından kullanılan hesapla aynı olmamasıdır.

Bir ortam değişkeninin geçerli değerini almak için, `cmd.exe` bir Windows işlem düğümünde veya `/bin/sh` Linux düğümünde başlatın:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh -c "printenv <ENV_VARIABLE_NAME>"`

## <a name="command-line-expansion-of-environment-variables"></a>Ortam değişkenlerinin komut satırı genişletmesi

İşlem düğümlerinde görevler tarafından yürütülen komut satırları bir kabuk altında çalışmaz. Bu, komut satırlarının ortam değişkeni genişletmesi (dahil) gibi kabuk özelliklerini yerel olarak kullanmayacağı anlamına gelir `PATH` . Bu tür özellikleri kullanmak için, kabuğu komut satırında çağırmanız gerekir. Örneğin, `cmd.exe` Windows işlem düğümlerinde veya `/bin/sh` Linux düğümlerinde başlatın:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c "MyTaskApplication $MY_ENV_VAR"`

## <a name="environment-variables"></a>Ortam değişkenleri

| Değişken adı                     | Description                                                              | Kullanılabilirlik | Örnek |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Görevin ait olduğu Batch hesabının adı.                  | Tüm görevler.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | Batch hesabının URL 'SI. | Tüm görevler. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Tüm uygulama paketi ortam değişkenlerinin ön eki. Örneğin, uygulama "FOO" sürümü "1" bir havuz üzerine yüklenirse, ortam değişkeni AZ_BATCH_APP_PACKAGE_FOO_1 (Linux üzerinde) veya AZ_BATCH_APP_PACKAGE_FOO # 1 (Windows üzerinde). AZ_BATCH_APP_PACKAGE_FOO_1, paketin indirildiği konuma (bir klasör) işaret eder. Uygulama paketinin varsayılan sürümünü kullanırken, sürüm numaraları olmadan AZ_BATCH_APP_PACKAGE ortam değişkenini kullanın. Linux 'ta ve uygulama paketi adı "Agent-Linux-x64" ise ve sürüm "1.1.46.0 ise ortam adı aslında: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, alt çizgi ve küçük harf kullanımı. Daha fazla bilgi için bkz. daha fazla ayrıntı için [yüklü uygulamaları yürütün](batch-application-packages.md#execute-the-installed-applications) . | İlişkili bir uygulama paketine sahip herhangi bir görev. Ayrıca, düğümün uygulama paketleri varsa tüm görevler için de kullanılabilir. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) veya AZ_BATCH_APP_PACKAGE_FOO # 1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Sınırlı bir Batch hizmeti işlemleri kümesine erişim veren bir kimlik doğrulama belirteci. Bu ortam değişkeni yalnızca, [görev eklendiğinde](/rest/api/batchservice/task/add#request-body) [authenticationtokensettings](/rest/api/batchservice/task/add#authenticationtokensettings) ayarlandıysa bulunur. Belirteç değeri, Batch API 'Lerinde, [batchclient. Open () .NET API](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_)'sinde olduğu gibi Batch istemcisi oluşturmak için kimlik bilgileri olarak kullanılır. | Tüm görevler. | OAuth2 erişim belirteci |
| AZ_BATCH_CERTIFICATES_DIR       | Bir [görev çalışma dizini](files-and-directories.md) içinde, sertifikaların Linux işlem düğümleri için depolandığı bir dizin. Bu ortam değişkeni Windows işlem düğümleri için geçerlidir.                                                  | Tüm görevler.   |  /mnt/Batch/Tasks/WorkItem/batchjob001/Job-1/task001/CERT |
| AZ_BATCH_HOST_LIST              | Biçimdeki [çok örnekli bir göreve](batch-mpi.md) ayrılan düğümlerin listesi `nodeIP,nodeIP` . | Çok örnekli birincil ve alt görevler. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Geçerli düğümün [çok örnekli bir görevin](batch-mpi.md)ana düğümü olup olmadığını belirtir. Olası değerler şunlardır `true` `false` .| Çok örnekli birincil ve alt görevler. | `true` |
| AZ_BATCH_JOB_ID                 | Görevin ait olduğu işin kimliği. | Başlangıç görevi dışındaki tüm görevler. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Düğümdeki iş hazırlama [görevi dizininin](files-and-directories.md) tam yolu. | Başlangıç görevi ve iş hazırlama görevi dışındaki tüm görevler. Yalnızca iş bir iş hazırlama göreviyle yapılandırılmışsa kullanılabilir. | C:\user\tasks\workıtem\jobprepreleasesamplejob\job-1\jobhazırlama |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Düğümdeki iş hazırlama [görevi çalışma dizininin](files-and-directories.md) tam yolu. | Başlangıç görevi ve iş hazırlama görevi dışındaki tüm görevler. Yalnızca iş bir iş hazırlama göreviyle yapılandırılmışsa kullanılabilir. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | [Çoklu örnek görevinin](batch-mpi.md) birincil görevinin çalıştırıldığı Işlem düğümünün IP adresi ve bağlantı noktası. MPı veya NCCL iletişimi için burada belirtilen bağlantı noktasını kullanmayın. Azure Batch hizmeti için ayrılmıştır. Bunun yerine MASTER_PORT değişkenini, komut satırı bağımsız değişkeni aracılığıyla geçirilen bir değer ile ayarlayarak (bağlantı noktası 6105 iyi bir varsayılan seçenektir) veya varsa AML kümeleri değerini kullanarak kullanın. | Çok örnekli birincil ve alt görevler. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | Görevin atandığı düğümün KIMLIĞI. | Tüm görevler. | TVM-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Varsa `true` , geçerli düğüm özel bir düğümdür. `false`Bu, [düşük öncelikli bir düğümdür](batch-low-pri-vms.md). | Tüm görevler. | `true` |
| AZ_BATCH_NODE_LIST              | Biçimdeki [çok örnekli bir göreve](batch-mpi.md) ayrılan düğümlerin listesi `nodeIP;nodeIP` . | Çok örnekli birincil ve alt görevler. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | Tüm bağlama dizinlerinin bulunduğu düğüm düzeyi [dosya sistemi bağlama](virtual-file-mount.md) konumunun tam yolu. Windows dosya paylaşımları bir sürücü harfi kullanır, bu nedenle Windows için bağlama sürücüsü cihazların ve sürücülerin bir parçasıdır.  |  Başlangıç görevi dahil tüm görevlerin Kullanıcı erişimi vardır ve Kullanıcı, bağlı dizin için bağlama izinlerinden haberdar olur. | Ubuntu 'da, örneğin, konum: `/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | Düğümdeki tüm [Batch dizinleri](files-and-directories.md) kökünün tam yolu. | Tüm görevler. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Düğümdeki [Paylaşılan dizinin](files-and-directories.md) tam yolu. Bir düğümde yürütülen tüm görevlerin bu dizine okuma/yazma erişimi vardır. Diğer düğümlerde yürütülen görevlerin bu dizine uzaktan erişimi yoktur ("paylaşılan" bir ağ dizini değildir). | Tüm görevler. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Düğümdeki [Başlangıç görevi dizininin](files-and-directories.md) tam yolu. | Tüm görevler. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Görevin çalıştığı havuzun kimliği. | Tüm görevler. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Düğümdeki [görev dizininin](files-and-directories.md) tam yolu. Bu dizin, `stdout.txt` `stderr.txt` görevi için ve ve AZ_BATCH_TASK_WORKING_DIR içerir. | Tüm görevler. | C:\user\tasks\workıtem\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Geçerli görevin kimliği. | Başlangıç görevi dışındaki tüm görevler. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Birincil görev ve [çok örnekli bir görevin](batch-mpi.md)her alt görevi için özdeş olan bir dizin yolu. Yol, çoklu örnek görevinin çalıştırıldığı her düğümde bulunur ve bu düğümde çalışan görev komutlarına okuma/yazma (hem [koordinasyon komutu](batch-mpi.md#coordination-command) hem de [uygulama komutu](batch-mpi.md#application-command)) erişilebilir. Diğer düğümlerde yürütülen alt görevler veya birincil görevin bu dizine uzaktan erişimi yoktur ("paylaşılan" bir ağ dizini değildir). | Çok örnekli birincil ve alt görevler. | C:\user\tasks\workıtemk\multiınstancesamplejob\job-1\multiınstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Düğümdeki [görev çalışma dizininin](files-and-directories.md) tam yolu. Şu anda çalışan görevin bu dizine okuma/yazma erişimi vardır. | Tüm görevler. | C:\user\tasks\workıtem\batchjob001\job-1\task001\wd |
| AZ_BATCH_TASK_WORKING_DIR       | Düğümdeki [görev çalışma dizininin](files-and-directories.md) tam yolu. Şu anda çalışan görevin bu dizine okuma/yazma erişimi vardır. | Tüm görevler. | C:\user\tasks\workıtem\batchjob001\job-1\task001\wd |
| AZ_BATCH_TASK_RESERVED_EPHEMERAL_DISK_SPACE_BYTES | VM 'nin üzerinde işaretlendiği disk alanının geçerli eşiği `DiskFull` . | Tüm görevler. | 1000000 |
| CCP_NODES                       | Bir [çok örnekli göreve](batch-mpi.md)ayrılan düğümlerin ve düğüm başına çekirdek sayısının listesi. Düğümler ve çekirdekler şu biçimde listelenmiştir `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, burada düğüm sayısının bir veya daha fazla düğüm IP adresi ve her biri için çekirdek sayısı gelir. |  Çok örnekli birincil ve alt görevler. |`2 10.0.0.4 1 10.0.0.5 1` |

## <a name="next-steps"></a>Sonraki adımlar

- [Batch ile ortam değişkenlerini nasıl kullanacağınızı](jobs-and-tasks.md#environment-settings-for-tasks)öğrenin.
- [Toplu işteki dosyalar ve dizinler](files-and-directories.md) hakkında daha fazla bilgi edinin
- [Çok örnekli görevler](batch-mpi.md)hakkında bilgi edinin.
