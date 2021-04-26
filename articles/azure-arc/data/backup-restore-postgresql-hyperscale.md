---
title: PostgreSQL için Azure Veritabanı Hiper Ölçek sunucu grupları için Yedekleme ve Geri Yükleme
description: PostgreSQL için Azure Veritabanı Hiper Ölçek sunucu grupları için Yedekleme ve Geri Yükleme
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686708"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure yay etkin PostgreSQL hiper ölçek sunucu gruplarını yedekleme ve geri yükleme

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunuzu yedekleyip veya geri yükledikten sonra, sunucu grubunuzun tüm PostgreSQL düğümlerinde tüm veritabanı kümesi yedeklenir ve/veya geri yüklenir.

## <a name="take-a-manual-full-backup"></a>El ile tam yedekleme yapın

Sunucu grubunuzun tüm veri ve günlük klasörlerinin tam yedeklemesini yapmak için aşağıdaki komutu çalıştırın:
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Konum:
- __ad__ , bir yedeklemenin adını gösterir
- __sunucu adı__ bir sunucu grubunu gösterir
- __No-wait__ , komut satırının bu komut satırı penceresini kullanmaya devam edebilmeniz için yedeklemenin tamamlanmasını bekmeyeceğini belirtir

Bu komut, Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunu oluşturan tüm düğümlerde dağıtılmış tam yedeklemeyi koordine edecektir. Diğer bir deyişle, düzenleyici ve çalışan düğüminizdeki tüm verileri yedekler.

Örnek:

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

Yedekleme tamamlandığında yedeklemenin KIMLIĞI, adı, boyutu, durumu ve zaman damgası döndürülür. Örnek:
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` yedeklemenin alındığı zaman dilimini belirtir. Bu örnekte, "+ 00:00" UTC saati (UTC + 00 saat 00 dakika) anlamına gelir.

> [!NOTE]
> Henüz şunları yapmak mümkün değildir:
> - Otomatik yedeklemeleri zamanla
> - Bir yedeklemenin alındığı sırada ilerlemesini göster

## <a name="list-backups"></a>Yedeklemeleri listeleme

Geri yüklenecek yedeklemeleri listelemek için aşağıdaki komutu çalıştırın:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Örnek:

```console
azdata arc postgres backup list --server-name postgres01
```

Şunun gibi bir çıktı döndürür:

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

Zaman damgası sütunu, yedeklemenin alındığı zaman UTC süresini gösterir.

## <a name="restore-a-backup"></a>Yedeği geri yükleme
Bu bölümde, tam geri yükleme veya zaman içinde bir nokta geri yükleme işlemlerinin nasıl yapılacağını gösteriyoruz. Tam bir yedeklemeyi geri yüklediğinizde, yedeğin tüm içeriğini geri yüklemeniz gerekir. Zaman içinde bir noktaya geri yükleme yaptığınızda, belirttiğiniz zaman noktaya geri yükleme yapabilirsiniz. Bu zaman içinde bu noktadan daha sonra gerçekleştirilen tüm işlemler geri yüklenmez.

### <a name="restore-a-full-backup"></a>Tam yedeklemeyi geri yükleme
Bir yedeğin tüm içeriğini geri yüklemek için şu komutu çalıştırın:
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

Konum:
- __yedekleme kimliği__ , yukarıda gösterilen liste yedekleme komutunda GÖSTERILEN yedeklemenin kimliğidir.
Bu, Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunu oluşturan tüm düğümlerde dağıtılan bir tam geri yüklemeyi koordine edecektir. Diğer bir deyişle, Düzenleyicinizdeki ve çalışan düğümlerdeki tüm verileri geri yükler.

#### <a name="examples"></a>Örnekler:

__Postgres01 sunucu grubunu kendi üzerine geri yükleyin:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Bu işlem yalnızca PostgreSQL sürüm 12 ve üzeri için desteklenir.

__Postgres01 sunucu grubunu farklı bir sunucu grubuna geri yükleyin postgres02:__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
Bu işlem, sürüm 11 ' i Başlatan tüm PostgreSQL sürümleri için desteklenir. Geri yükleme işleminden önce hedef sunucu grubu oluşturulmalıdır, aynı yapılandırma olmalıdır ve kaynak sunucu grubuyla aynı yedek PVC 'yi kullanıyor olmalıdır.

Geri yükleme işlemi tamamlandığında komut satırına aşağıdakine benzer bir çıktı döndürür:

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> Henüz şunları yapmak mümkün değildir:
> - Adını belirterek bir yedeği geri yükleme
> - Geri yükleme işleminin ilerlemesini göster


### <a name="do-a-point-in-time-restore"></a>Zaman içinde bir noktaya geri yükleme

Bir sunucu grubunu belirli bir zaman noktasına geri yüklemek için şu komutu çalıştırın:
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

Restore komutunun genel biçimini okumak için şunu çalıştırın: `azdata arc postgres backup restore --help` .

Burada, `time` geri yükleme için zaman noktasıdır. Bir zaman damgası ya da bir sayı ve sonek ( `m` dakikalar için, `h` `d` günler için veya haftalar için) sağlayın `w` . Örneğin, `1.5h` 90 dakika geri gider.

#### <a name="examples"></a>Örnekler:
__Sunucu grubu postgres01 için bir zaman noktası geri yüklemesi yapın:__

Bir sunucu grubunun kendisine zaman içinde geri yüklenmesi mümkün değildir.

__Postgres01 sunucu grubunun belirli bir zaman damgasına postgres02 farklı bir sunucu grubuna geri yüklemesini zaman bir noktada yapın:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

Bu örnek, sunucu grubu postgres01 'nin 2020 Aralık ' de, 04:23:48.75 UTC 'de olduğu durum postgres02 sunucu grubuna geri yükler. "+ 00" öğesinin, gösterdiğiniz zaman dilimini belirtir. Bir saat dilimi belirtmezseniz, geri yükleme işlemini çalıştırdığınız istemcinin saat dilimi kullanılacaktır.

Örnek:
- `2020-12-08 04:23:48.751326+00`UTC olarak yorumlanır `2020-12-08 04:23:48.751326`
- Pasifik standart saat dilimindeyse (PST = UTC + 08), `2020-12-08 04:23:48.751326` UTC olarak yorumlanır `2020-12-08 12:23:48.751326` . Bu işlem, sürüm 11 ' i Başlatan tüm PostgreSQL sürümleri için desteklenir. Hedef sunucu grubu geri yükleme işleminden önce oluşturulmalı ve kaynak sunucu grubuyla aynı yedek PVC 'yi kullanıyor olmalıdır.


__Sunucu grubu postgres01 farklı bir sunucu grubuna postgres02, geçmişteki belirli bir süre için bir zaman noktası geri yüklemesi yapın:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

Bu örnek postgres02 sunucu grubuna geri yükleme, sunucu grubu postgres01 'ın 22 dakika önce olduğu durumdur.
Bu işlem, sürüm 11 ' i Başlatan tüm PostgreSQL sürümleri için desteklenir. Hedef sunucu grubu geri yükleme işleminden önce oluşturulmalı ve kaynak sunucu grubuyla aynı yedek PVC 'yi kullanıyor olmalıdır.

> [!NOTE]
> Henüz şunları yapmak mümkün değildir:
> - Geri yükleme işleminin ilerlemesini göster

## <a name="delete-backups"></a>Yedekleri silme

Yedekleme bekletme önizlemede ayarlanamıyor. Ancak, ihtiyacınız olmayan yedeklemeleri el ile silebilirsiniz.
Yedeklemeleri silmenin genel komutu:

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

burada:
- `--server-name` kullanıcının bir yedeklemeyi silmek istediği sunucu grubunun adıdır
- `--name` Silinecek yedeğin adı
- `-id`Silinecek yedeğin KIMLIĞI

> [!NOTE]
> `--name` ve `-id` birbirini dışlıyor.

Örnek:

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

Önceki paragrafta açıklandığı gibi, liste yedekleme komutunu çalıştırarak yedeklemelerinizin adını ve KIMLIĞINI elde edebilirsiniz.

Delete komutu hakkında daha fazla ayrıntı için şunu çalıştırın:

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Sonraki adımlar
- Ölçek Genişletme [(çalışan düğümleri ekleme)](scale-out-postgresql-hyperscale-server-group.md) hakkında bilgi edinmek için sunucu grubunuz
- Sunucu grubunuzu [ölçeği artırma veya azaltma (bellek/sanal çekirdekleri artırma/azaltma)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) hakkında bilgi edinin
