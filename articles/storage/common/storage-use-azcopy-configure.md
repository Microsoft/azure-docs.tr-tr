---
title: AzCopy (Azure depolama) içindeki günlüklere sahip işleri & hatalar bulun | Microsoft Docs
description: Hataları tanılamak ve plan dosyalarını kullanarak duraklatılan işleri sürdürmeyi sağlamak için günlükleri nasıl kullanacağınızı öğrenin.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d3b956803e9a796c49288f90873e88c3b69f1c7b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502904"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>AzCopy içindeki log ve plan dosyalarını kullanarak hataları bulma ve işleri sürdürme

AzCopy, bir depolama hesabına iki yönlü blob veya dosya kopyalama işlemi gerçekleştirmenizi sağlayan bir komut satırı yardımcı programıdır. Bu makale, hataları tanılamak için günlükleri kullanmanıza yardımcı olur ve sonra işleri sürdürmeniz için dosyaları plan olarak kullanabilirsiniz. Bu makalede ayrıca, ayrıntı düzeylerini ve depolandıkları varsayılan konumu değiştirerek günlük ve plan dosyalarının nasıl yapılandırılacağı gösterilmektedir.

> [!NOTE]
> AzCopy kullanmaya başlamanıza yardımcı olacak içerik arıyorsanız, bkz. [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md).

## <a name="log-and-plan-files"></a>Dosyaları günlüğe kaydet ve planla

AzCopy her iş için *günlük* ve *plan* dosyaları oluşturur. Bu günlükleri, olası sorunları araştırmak ve gidermek için kullanabilirsiniz. 

Günlükler hatanın durumunu ( `UPLOADFAILED` , `COPYFAILED` , ve `DOWNLOADFAILED` ), tam yolu ve hatanın nedenini içerecektir.

Varsayılan olarak, günlük ve plan dosyaları `%USERPROFILE%\.azcopy` `$HOME$\.azcopy` Mac ve Linux üzerindeki Windows veya dizindeki dizinde bulunur, ancak bu konumu değiştirebilirsiniz. 

İlgili hata, dosyada görüntülenen ilk hata değildir. Ağ hataları, zaman aşımları ve sunucu meşgul hataları gibi hatalar için AzCopy en fazla 20 kez yeniden dener ve genellikle yeniden deneme işlemi başarılı olur.  Gördüğünüz ilk hata, başarıyla yeniden denenen bir zararsız olabilir.  Bu nedenle, dosyadaki ilk hataya bakmak yerine, veya yakınındaki hataları arayın `UPLOADFAILED` `COPYFAILED` `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Microsoft Desteği bir istek gönderirken (veya herhangi bir üçüncü taraf ile ilgili sorunu gidermeye çalıştığınızda), yürütmek istediğiniz komutun Redaksiyonu yapılmış sürümünü paylaşabilirsiniz. Bu, SAS 'nin yanlışlıkla herhangi bir gövdele paylaşılmamasını sağlar. Redaksiyonu yapılmış sürümü günlük dosyasının başlangıcında bulabilirsiniz.

## <a name="review-the-logs-for-errors"></a>Günlükleri hatalara karşı gözden geçirin

Aşağıdaki komut, günlükteki durum ile tüm hataları alacak `UPLOADFAILED` `04dc9ca9-158f-7945-5933-564021086c79` :

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>İşleri görüntüleme ve sürdürme

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

> [!TIP]
> SAS belirteci gibi yol bağımsız değişkenlerini tek tırnak işareti (' ') ile çevrele. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

Bir işi sürdürürseniz AzCopy iş planı dosyasına bakar. Plan dosyası, iş ilk oluşturulduğunda işleme için tanımlanan tüm dosyaları listeler. Bir işi sürdürürseniz AzCopy, önceden aktarılmayan plan dosyasında listelenen tüm dosyaları aktarmaya çalışır.

## <a name="change-the-location-of-plan-files"></a>Plan dosyalarının konumunu değiştirme

Bu komutlardan herhangi birini kullanın.

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> Komut isteminde şunu kullanın:: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

`azcopy env`Bu değişkenin geçerli değerini denetlemek için öğesini kullanın. Değer boşsa, plan dosyaları varsayılan konuma yazılır.

## <a name="change-the-location-of-log-files"></a>Günlük dosyalarının konumunu değiştirme

Bu komutlardan herhangi birini kullanın.

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_LOG_LOCATION="<value>"` <br> Komut isteminde şunu kullanın:: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

`azcopy env`Bu değişkenin geçerli değerini denetlemek için öğesini kullanın. Değer boşsa, günlükler varsayılan konuma yazılır.

## <a name="change-the-default-log-level"></a>Varsayılan günlük düzeyini değiştirme

Varsayılan olarak, AzCopy günlük düzeyi olarak ayarlanır `INFO` . Disk alanından tasarruf etmek için günlük ayrıntı düzeyini azaltmak isterseniz, seçeneğini kullanarak bu ayarın üzerine yazın ``--log-level`` . 

Kullanılabilir günlük düzeyleri şunlardır: `NONE` , `DEBUG` , `INFO` , `WARNING` , `ERROR` , `PANIC` , ve `FATAL` .

## <a name="remove-plan-and-log-files"></a>Planı ve günlük dosyalarını kaldır

Disk alanını kazanmak için yerel makinenizden tüm planı ve günlük dosyalarını kaldırmak istiyorsanız `azcopy jobs clean` komutunu kullanın.

Yalnızca bir işle ilişkili planı ve günlük dosyalarını kaldırmak için kullanın `azcopy jobs rm <job-id>` . `<job-id>`Bu örnekteki yer tutucuyu işin Iş kimliğiyle değiştirin.

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
