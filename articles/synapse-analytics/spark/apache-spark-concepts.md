---
title: Apache Spark temel kavramlar
description: Azure SYNAPSE Analytics 'te Apache Spark için temel kavramlara giriş.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 51b2e8cd968c4c14777d196d90686b13158aef42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120317"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Azure SYNAPSE Analytics temel kavramları Apache Spark

Apache Spark, büyük veri analizi uygulamalarının performansını artırmak üzere bellek içi işlemeyi destekleyen paralel bir işleme çerçevesidir. Azure SYNAPSE Analytics 'te Apache Spark, Microsoft 'un buluttaki Apache Spark uygulamalarından biridir. 

Azure SYNAPSE, Azure 'da Spark özellikleri oluşturmayı ve yapılandırmayı kolaylaştırır. Azure SYNAPSE, burada belgelenen Bu Spark yeteneklerinin farklı bir uygulamasını sağlar.

## <a name="spark-pools"></a>Spark havuzları

Azure portal bir sunucusuz Apache Spark havuzu oluşturulur. Bu, bir Spark havuzunun, verileri işleyen bir Spark örneği oluşturmak için kullanıldığı bir tanımıdır. Bir Spark havuzu oluşturulduğunda, bu yalnızca meta veriler olarak bulunur ve hiçbir kaynak Tüketilmekte, çalışmıyor veya ücretlendirililmez. Spark havuzunda Spark örneğinin özelliklerini denetleyen bir dizi özellik vardır. Bu özellikler, ad, boyut, ölçekleme davranışı ve yaşam süresi ile sınırlı değildir.

Spark havuzları oluşturmayla ilişkili dolar veya kaynak maliyeti olmadığından, herhangi bir sayıda farklı yapılandırma ile herhangi bir numara oluşturulabilir. İzinler, kullanıcıların yalnızca bazılarına erişime izin veren Spark havuzlarına da uygulanabilir.

En iyi uygulama, geliştirme ve hata ayıklama için kullanılabilecek daha küçük Spark havuzları oluşturmak ve ardından üretim iş yüklerini çalıştırmaya yönelik daha büyük bir uygulamadır.

Spark havuzu oluşturmayı ve tüm özelliklerini buradan görmenizi [Azure SYNAPSE Analytics 'Te Spark havuzlarıyla çalışmaya başlama](../quickstart-create-apache-spark-pool-portal.md) hakkında bilgi edinebilirsiniz

## <a name="spark-instances"></a>Spark örnekleri

Spark örnekleri, bir Spark havuzuna bağlandığınızda, bir oturum oluşturup bir iş çalıştırdığınızda oluşturulur. Birden çok kullanıcının tek bir Spark havuzuna erişimi olabileceğinden, bağlanan her kullanıcı için yeni bir Spark örneği oluşturulur. 

İkinci bir işi gönderdiğinizde, havuzda kapasite varsa, var olan Spark örneğinin kapasitesi de vardır. Ardından, var olan örnek işi işleyecek. Aksi takdirde, kapasite havuz düzeyinde kullanılabiliyorsa yeni bir Spark örneği oluşturulur.

## <a name="examples"></a>Örnekler

### <a name="example-1"></a>Örnek 1

- SP1 adlı bir Spark havuzu oluşturursunuz; 20 düğümden oluşan sabit bir küme boyutuna sahiptir.
- Bir not defteri işi gönderirseniz, iş işlemek için bir Spark örneği olan 10 düğümleri kullanan bir J1, SI1 oluşturulur.
- Artık havuzda kapasite olduğundan ve örnek, J2, SI1 tarafından işlendiği için, daha sonra 10 düğümü kullanan başka bir iş J2.
- J2 11 düğümleri isteniyorsa, SP1 veya SI1 'de kapasite yoktur. Bu durumda, J2 bir not defterinden geliyorsa iş reddedilir; J2 bir Batch işinden geliyorsa sıraya alınır.

### <a name="example-2"></a>Örnek 2

- Spark havuz çağrısı SP2 oluşturursunuz; Otomatik ölçeklendirme etkinleştirilmiş 10 – 20 düğümleri vardır
- İşi işlemek için bir Spark örneği, SI1 olan J1 bir not defteri işi gönderebilirsiniz.
- Artık, örnek otomatik olarak 20 düğüme ve J2 işleme olan havuzda kapasite olduğundan, J2.

### <a name="example-3"></a>Örnek 3

- SP1 adlı bir Spark havuzu oluşturursunuz; 20 düğümden oluşan sabit bir küme boyutuna sahiptir.
- Bir not defteri işi gönderirseniz, iş işlemek için bir Spark örneği olan 10 düğümleri kullanan bir J1, SI1 oluşturulur.
- Farklı bir kullanıcı olan J3, işi işlemek için yeni bir Spark örneği, SI2 olan bir Işi gönderir.
- Artık havuzda kapasite olduğundan ve örnek J2, SI1 tarafından işlendiği için, J2 tarafından kullanılan başka bir iş (10 düğüm) gönderebilirsiniz.

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Azure SYNAPSE için Apache Spark kotalar ve kaynak kısıtlamaları

### <a name="workspace-level"></a>Çalışma alanı düzeyi

Her Azure SYNAPSE çalışma alanı, Spark için kullanılabilecek varsayılan sanal çekirdek kotasıyla gelir. Kullanım deseninin çalışma alanındaki tüm Vçekirdekleri kullanması için kota, Kullanıcı kotası ve veri akışı kotası arasında bölünür. Kota, aboneliğinizin türüne göre farklılık, ancak kullanıcı ile veri akışı arasında simetrik bir şekilde yapılır. Ancak, çalışma alanında kalanından daha fazla sanal çekirdek istemeniz durumunda aşağıdaki hatayı alırsınız:

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

İletideki bağlantı bu makaleye işaret eder.

Aşağıdaki makalede, çalışma alanı vCore kotasında bir artış isteme açıklanmaktadır.

- Hizmet türü olarak "Azure SYNAPSE Analytics" i seçin.
- Kota ayrıntıları penceresinde, çalışma alanı başına Apache Spark (vCore) öğesini seçin

[Azure portal aracılığıyla kapasite artışı isteyin](../../azure-portal/supportability/per-vm-quota-requests.md#request-a-standard-quota-increase-from-help--support)

### <a name="spark-pool-level"></a>Spark havuz düzeyi

Bir Spark havuzu tanımladığınızda, bu havuz için Kullanıcı başına bir kota tanımlayın. birden çok not defteri veya iş veya 2 karışımı çalıştırırsanız, havuz kotasını tüketme olasılığı vardır. Bunu yaparsanız, aşağıdakine benzer bir hata iletisi oluşturulacaktır

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

Bu sorunu çözmek için, bir not defteri veya işi çalıştırarak yeni bir kaynak isteği göndermeden önce havuz kaynaklarının kullanımını azaltmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark belgeleri](https://spark.apache.org/docs/2.4.5/)