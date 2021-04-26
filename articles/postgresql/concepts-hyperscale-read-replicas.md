---
title: Çoğaltmaları oku-PostgreSQL için Azure veritabanı-hiper ölçek (Citus)
description: Bu makalede PostgreSQL için Azure veritabanı-hiper ölçek (Citus) içindeki çoğaltma okuma özelliği açıklanır.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cf8c64bf3858f0d3b7d633b94bc7302fbe563f87
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024002"
---
# <a name="read-replicas-in-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda çoğaltmaları okuma-hiper ölçek (Citus)

> [!IMPORTANT]
> Hyperscale (Citus) içindeki okuma çoğaltmaları Şu anda önizlemededir. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
>
> Diğer yeni özelliklerin bir listesini, [hiper ölçek için Önizleme özellikleri (Citus)](hyperscale-preview-features.md)sayfasına bakabilirsiniz.

Çoğaltma oku özelliği, bir Hyperscale (Citus) sunucu grubundan verileri salt bir sunucu grubuna çoğaltmanıza olanak sağlar. Çoğaltmalar, PostgreSQL fiziksel çoğaltma teknolojisiyle **zaman uyumsuz** olarak güncelleştirilir. Birincil sunucudan sınırsız sayıda çoğaltmaya çoğaltma yapabilirsiniz.

Çoğaltmalar, normal Hyperscale (Citus) sunucu gruplarıyla benzer şekilde yönettiğiniz yeni sunucu gruplarıdır. Her okuma çoğaltması için, sanal çekirdekler ve depolama biriminde GB/ay içinde sağlanan işlem için faturalandırılırsınız.

[Çoğaltmaları oluşturma ve yönetme](howto-hyperscale-read-replicas-portal.md)hakkında bilgi edinin.

## <a name="when-to-use-a-read-replica"></a>Okuma çoğaltması ne zaman kullanılır?

Okuma çoğaltması özelliği, okuma yoğunluklu iş yüklerinin performansını ve ölçeğini artırmaya yardımcı olur. Okuma iş yükleri çoğaltmalar için yalıtılabilir, ancak yazma iş yükleri birincilde yönlendirilebilir.

Yaygın bir senaryo, bı ve analitik iş yüklerinin raporlama için veri kaynağı olarak okuma çoğaltmasını kullanmasını sağlar.

Çoğaltmalar salt okunurdur, birincil üzerindeki yazma kapasitesini doğrudan azaltmazlar.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

Özelliği, çoğaltma gecikmesi kabul edilebilir olan ve sorgu boşaltma için amaçlanmış senaryolar için tasarlanmıştır. Çoğaltma verilerinin güncel olması beklenen zaman uyumlu çoğaltma senaryolarına yönelik değildir. Birincil ve çoğaltma arasında ölçülebilir bir gecikme olacaktır. Gecikme, iş yüküne ve birincil ve çoğaltma arasındaki gecikme süresine bağlı olarak dakika veya saat olabilir.  Çoğaltılan veriler, sonunda birincil verilerle tutarlı hale gelir. Bu gecikmeyi barındırabilecek iş yükleri için bu özelliği kullanın. 

## <a name="create-a-replica"></a>Çoğaltma oluşturma

Çoğaltma oluşturma iş akışını başlattığınızda boş bir hiper ölçek (Citus) sunucu grubu oluşturulur. Yeni Grup, birincil sunucu grubunda bulunan verilerle doldurulur. Oluşturulma Zamanı, birincil ve son haftalık tam yedeklemeden bu yana geçen süre miktarına bağlıdır. Süre, birkaç dakika ile birkaç saat arasında değişebilir.

Okuma çoğaltması özelliği, mantıksal çoğaltma değil PostgreSQL fiziksel çoğaltmasını kullanır. Varsayılan mod, çoğaltma yuvaları kullanılarak akış çoğaltmasıyla yapılır.
Gerektiğinde, günlük aktarma işlemi yakalamak için kullanılır.

[Azure Portal bir okuma çoğaltması oluşturmayı](howto-hyperscale-read-replicas-portal.md)öğrenin.

## <a name="connect-to-a-replica"></a>Bir çoğaltmaya bağlanma

Bir çoğaltma oluşturduğunuzda, birincil sunucu grubu güvenlik duvarı kuralları aktarılmaz. Bu kuralların çoğaltma için bağımsız olarak ayarlanması gerekir.

Çoğaltma, birincil sunucu grubundan yönetici ("citus") hesabını devralır.
Tüm Kullanıcı hesapları, okuma çoğaltmalarına çoğaltılır. Bir okuma çoğaltmasına yalnızca birincil sunucuda bulunan Kullanıcı hesaplarını kullanarak bağlanabilirsiniz.

Bir normal Hyperscale (Citus) sunucu grubunda yaptığınız gibi, onun ana bilgisayar adını ve geçerli bir kullanıcı hesabını kullanarak çoğaltmanın düzenleyici düğümüne bağlanabilirsiniz.
Yönetici Kullanıcı adı **citus** olan **çoğaltmamın** adlı bir sunucu için psql kullanarak çoğaltmanın düzenleyici düğümüne bağlanabilirsiniz:

```bash
psql -h c.myreplica.postgres.database.azure.com -U citus@myreplica -d postgres
```

İstemde, Kullanıcı hesabının parolasını girin.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Bu bölümde çoğaltma oku özelliği hakkında dikkat edilecek noktalar özetlenmektedir.

### <a name="new-replicas"></a>Yeni çoğaltmalar

Bir okuma çoğaltması yeni bir Hyperscale (Citus) sunucu grubu olarak oluşturulur. Var olan bir sunucu grubu bir çoğaltmaya yapılamaz. Başka bir okuma çoğaltmasının çoğaltmasını oluşturamazsınız.

### <a name="replica-configuration"></a>Çoğaltma yapılandırması

Çoğaltma, birincil olarak aynı işlem, depolama ve çalışan düğüm ayarları kullanılarak oluşturulur. Bir çoğaltma oluşturulduktan sonra, depolama ve yedekleme saklama süresi dahil olmak üzere çeşitli ayarlar değiştirilebilir. Çoğaltmalarda, depolama boyutu ve çalışan düğümlerinin sayısı gibi diğer ayarlar değiştirilemez.

Çoğaltmaları, birinciden gelen değişiklikleri tutmak için yeterince güçlü tutmayı unutmayın. Örneğin, birincil üzerinde ölçeklendirirseniz çoğaltmalarda işlem gücünü ölçeklendirdiğinizden emin olun.

Çoğaltma oluşturulduğunda veya daha sonra güvenlik duvarı kuralları ve parametre ayarları birincil sunucudan çoğaltmaya devralınmaz.

### <a name="regions"></a>Bölgeler

Hyperscale (Citus) sunucu grupları yalnızca aynı bölge çoğaltmasını destekler.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Portal okuma çoğaltmaları oluşturmayı ve yönetmeyi](howto-hyperscale-read-replicas-portal.md)öğrenin.
