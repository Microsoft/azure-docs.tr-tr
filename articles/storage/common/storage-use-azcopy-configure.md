---
title: Azure Storage ile AzCopy 'i yapılandırma, iyileştirme ve sorunlarını giderme | Microsoft Docs
description: AzCopy 'i yapılandırın, iyileştirin ve sorun giderin.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 2b3fcba755c9ddb28e37400c5cba790ed0df41b9
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595137"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy 'i yapılandırma, iyileştirme ve sorun giderme

AzCopy, bir depolama hesabına blob veya dosya kopyalamak için kullanabileceğiniz bir komut satırı yardımcı programıdır. Bu makale, gelişmiş yapılandırma görevlerini gerçekleştirmenize yardımcı olur ve AzCopy kullandığınızda oluşabilecek sorunları gidermenize yardımcı olur.

> [!NOTE]
> AzCopy kullanmaya başlamanıza yardımcı olacak içerik arıyorsanız, aşağıdaki makalelerden birine bakın:
> - [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)
> - [AzCopy ve BLOB Storage ile veri aktarma](storage-use-azcopy-blobs.md)
> - [AzCopy ve dosya depolama ile veri aktarma](storage-use-azcopy-files.md)
> - [AzCopy ve Amazon S3 demetleri ile veri aktarma](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Ara sunucu ayarlarını yapılandırma

AzCopy proxy ayarlarını yapılandırmak için `https_proxy` ortam değişkenini ayarlayın. AzCopy 'i Windows üzerinde çalıştırırsanız AzCopy proxy ayarlarını otomatik olarak algılar, bu nedenle bu ayarı Windows 'ta kullanmanız gerekmez. Bu ayarı Windows 'ta kullanmayı seçerseniz, otomatik algılamayı geçersiz kılar.

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | Komut isteminde şunu kullanın: `set https_proxy=<proxy IP>:<proxy port>`<br> PowerShell kullanım: `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Şu anda AzCopy, NTLM veya Kerberos ile kimlik doğrulaması gerektiren proxy 'leri desteklemiyor.

## <a name="optimize-performance"></a>Performansı iyileştirme

Performans ve kaynak tüketimine ilişkin en iyi zorunluluğunu getirir bulmak için, performansı ve ardından komut ve ortam değişkenlerini kullanabilirsiniz.

### <a name="run-benchmark-tests"></a>Kıyaslama testlerini Çalıştır

Genel performans istatistiklerini görüntülemek ve kimlik performans sorunlarını gidermek için belirli blob kapsayıcıları üzerinde bir performans kıyaslama testi çalıştırabilirsiniz. 

> [!NOTE]
> Geçerli sürümde, bu özellik yalnızca BLOB depolama kapsayıcıları için kullanılabilir.

Bir performans kıyaslama testi çalıştırmak için aşağıdaki komutu kullanın.

|    |     |
|--------|-----------|
| **Sözdizimi** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Örnek** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/'` |

Bu komut test verilerini belirtilen bir hedefe yükleyerek bir performans kıyaslaması çalıştırır. Test verileri bellekte oluşturulur, hedefe yüklenir ve ardından test tamamlandıktan sonra hedefteki silinir. İsteğe bağlı komut parametrelerini kullanarak oluşturulacak dosya sayısını ve bunların kaç boyutunu istediğinizi belirtebilirsiniz.

Bu komuta ilişkin ayrıntılı yardım kılavuzunu görüntülemek için `azcopy bench -h` yazın ve ENTER tuşuna basın.

### <a name="optimize-throughput"></a>Aktarım hızını iyileştirme

Verimlilik veri hızına bir tavan koymak için `cap-mbps` bayrağını kullanabilirsiniz. Örneğin, aşağıdaki komut, saniye başına `10` megabit (MB) olarak büyük/küçük harf işleme sağlar.

```azcopy
azcopy cap-mbps 10
```

Küçük dosyalar aktarılırken üretilen iş azalabilir. @No__t_0 ortam değişkenini ayarlayarak aktarım hızını artırabilirsiniz. Bu değişken, gerçekleşebileceğini eşzamanlı isteklerin sayısını belirtir.  

Bilgisayarınızda 5 ' ten az CPU varsa, bu değişkenin değeri `32` olarak ayarlanır. Aksi takdirde, varsayılan değer 16 ' ya eşittir CPU sayısıyla çarpılır. Bu değişkenin en büyük varsayılan değeri `3000` ' dır, ancak bu değeri el ile veya daha düşük bir şekilde ayarlayabilirsiniz. 

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Bu değişkenin geçerli değerini denetlemek için `azcopy env` kullanın. Değer boşsa, herhangi bir AzCopy günlük dosyasının başlangıcına bakarak hangi değerin kullanıldığını okuyabilirsiniz. Seçili değer ve seçildiği neden burada raporlanır.

Bu değişkeni ayarlamadan önce, bir kıyaslama testi çalıştırmanızı öneririz. Kıyaslama test süreci, önerilen eşzamanlılık değerini rapor eder. Alternatif olarak, ağ koşullarınız ve yüklerinizin farklılık göstermesi durumunda, bu değişkeni belirli bir sayı yerine `AUTO` sözcüğüne ayarlayın. Bu, AzCopy 'in kıyaslama testlerinde kullandığı otomatik ayarlama işlemini her zaman çalıştırmasına neden olur.

### <a name="optimize-memory-use"></a>Bellek kullanımını iyileştirme

Dosya indirme ve karşıya yükleme sırasında AzCopy 'in kullanmasını istediğiniz en fazla sistem belleği miktarını belirtmek için `AZCOPY_BUFFER_GB` ortam değişkenini ayarlayın.
Bu değeri gigabayt (GB) cinsinden ifade edin.

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

## <a name="troubleshoot-issues"></a>Sorunları giderme

AzCopy her iş için günlük ve plan dosyaları oluşturur. Olası sorunları araştırmak ve sorunlarını gidermek için günlükleri kullanabilirsiniz. 

Günlüklerde hata durumu (`UPLOADFAILED`, `COPYFAILED` ve `DOWNLOADFAILED`), tam yol ve hatanın nedeni yer alacak.

Varsayılan olarak, günlük ve plan dosyaları Mac ve Linux üzerindeki Windows veya `$HOME$\.azcopy` dizinindeki `%USERPROFILE$\.azcopy` dizininde bulunur, ancak isterseniz bu konumu değiştirebilirsiniz.

> [!IMPORTANT]
> Microsoft Desteği bir istek gönderirken (veya herhangi bir üçüncü taraf ile ilgili sorunu gidermeye çalıştığınızda), yürütmek istediğiniz komutun Redaksiyonu yapılmış sürümünü paylaşabilirsiniz. Bu, SAS 'nin yanlışlıkla herhangi bir gövdele paylaşılmamasını sağlar. Redaksiyonu yapılmış sürümü günlük dosyasının başlangıcında bulabilirsiniz.

### <a name="review-the-logs-for-errors"></a>Günlükleri hatalara karşı gözden geçirin

Aşağıdaki komut, `04dc9ca9-158f-7945-5933-564021086c79` günlüğünden `UPLOADFAILED` durumundaki tüm hataları alacak:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>İşleri görüntüleme ve sürdürme

Her aktarım işlemi bir AzCopy işi oluşturur. İşlerin geçmişini görüntülemek için aşağıdaki komutu kullanın:

```
azcopy jobs list
```

İş istatistiklerini görüntülemek için aşağıdaki komutu kullanın:

```
azcopy jobs show <job-id>
```

Aktarımları duruma göre filtrelemek için aşağıdaki komutu kullanın:

```
azcopy jobs show <job-id> --with-status=Failed
```

Başarısız/iptal edildi bir işi yeniden başlatmak için aşağıdaki komutu kullanın. Bu komut, güvenlik nedenleriyle kalıcı olmadığından, kimliğini SAS belirteciyle birlikte kullanır:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Bir işi sürdürürseniz AzCopy iş planı dosyasına bakar. Plan dosyası, iş ilk oluşturulduğunda işleme için tanımlanan tüm dosyaları listeler. Bir işi sürdürürseniz AzCopy, önceden aktarılmayan plan dosyasında listelenen tüm dosyaları aktarmaya çalışır.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Planın ve günlük dosyalarının konumunu değiştirme

Varsayılan olarak, plan ve günlük dosyaları Windows 'daki `%USERPROFILE$\.azcopy` dizininde veya Mac ve Linux 'ta `$HOME$\.azcopy` dizininde bulunur. Bu konumu değiştirebilirsiniz.

### <a name="change-the-location-of-plan-files"></a>Plan dosyalarının konumunu değiştirme

Bu komutlardan herhangi birini kullanın.

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Bu değişkenin geçerli değerini denetlemek için `azcopy env` kullanın. Değer boşsa, plan dosyaları varsayılan konuma yazılır.

### <a name="change-the-location-of-log-files"></a>Günlük dosyalarının konumunu değiştirme

Bu komutlardan herhangi birini kullanın.

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Bu değişkenin geçerli değerini denetlemek için `azcopy env` kullanın. Değer boşsa, günlükler varsayılan konuma yazılır.

## <a name="change-the-default-log-level"></a>Varsayılan günlük düzeyini değiştirme

Varsayılan olarak, AzCopy günlük düzeyi `INFO` olarak ayarlanır. Disk alanını kaydetmek için günlük ayrıntı düzeyini azaltmak isterseniz, ``--log-level`` seçeneğini kullanarak bu ayarın üzerine yazın. 

Kullanılabilir günlük düzeyleri şunlardır: `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` ve `FATAL`.

## <a name="remove-plan-and-log-files"></a>Planı ve günlük dosyalarını kaldır

Disk alanını kazanmak için yerel makinenizden tüm planı ve günlük dosyalarını kaldırmak istiyorsanız, `azcopy jobs clean` komutunu kullanın.

Yalnızca bir işle ilişkili planı ve günlük dosyalarını kaldırmak için `azcopy jobs rm <job-id>` kullanın. Bu örnekteki `<job-id>` yer tutucusunu işin iş kimliğiyle değiştirin.


