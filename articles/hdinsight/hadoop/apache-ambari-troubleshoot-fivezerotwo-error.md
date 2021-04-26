---
title: Azure HDInsight 'ta Apache ambarı Kullanıcı arabirimi 502 hatası
description: Azure HDInsight kümenize erişmeye çalıştığınızda Apache ambarı Kullanıcı arabirimi 502 hatası
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 750232b19bd5ef0674a9df79fdf3972a679eda7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946793"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta Apache ambarı Kullanıcı arabirimi 502 hatası

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

HDInsight kümeniz için Apache ambarı Kullanıcı arabirimine erişmeyi denediğinizde şuna benzer bir ileti alırsınız: "502-Web sunucusu bir ağ geçidi veya proxy sunucu işlevi gören geçersiz bir yanıt aldı."

## <a name="cause"></a>Nedeni

Genel olarak, HTTP 502 durum kodu, ambarı sunucusunun etkin yayın düğümünde düzgün çalışmadığı anlamına gelir. Olası birkaç temel neden vardır.

## <a name="resolution"></a>Çözüm

Çoğu durumda, sorunu hafifletmek için etkin headnode 'u yeniden başlatabilirsiniz. Ya da headnode için daha büyük bir VM boyutu seçin.

### <a name="ambari-server-failed-to-start"></a>Ambarı sunucusu başlatılamadı

Ambarı sunucusunun neden başlayamadığına ilişkin bilgi edinmek için, ambarı-sunucu günlüklerine bakabilirsiniz. Bunun yaygın bir nedeni, veritabanı tutarlılık denetimi hatasıdır. Bunu şu günlük dosyasında bulabilirsiniz: `/var/log/ambari-server/ambari-server-check-database.log` .

Küme düğümünde herhangi bir değişiklik yaptıysanız lütfen bunları geri alın. Herhangi bir Hadoop/Spark ile ilgili yapılandırmayı değiştirmek için her zaman ambarı Kullanıcı arabirimini kullanın.

### <a name="ambari-server-taking-100-cpu-utilization"></a>%100 CPU kullanımı alan ambarı sunucusu

Nadir durumlarda, ambarı-sunucu işleminin sürekli %100 CPU kullanımına yakın olduğunu gördük. Risk azaltma olarak, etkin yayın düğümüne SSH oluşturabilir ve bu işlemi, ambarı sunucu sürecini sonlandırılıp yeniden başlatabilirsiniz.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambarı sunucusu OOM tarafından sonlandırıldı-Killer

Bazı senaryolarda, baş düğümüne belleği tükenmektedir ve Linux OOM-Killer, sonlandırılmaya yönelik işlem seçer. Bu durumu, bulunamayan AmbariServer işlem KIMLIĞINI arayarak doğrulayabilirsiniz. Ardından `/var/log/syslog` , aşağıdaki gibi bir şey olup olmadığına bakın:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Ardından, hangi işlemlerin anımı aldığını belirleyip daha sonra kök nedeni deneyin.

### <a name="other-issues-with-ambari-server"></a>Ambarı sunucusuyla ilgili diğer sorunlar

Genellikle, ambarı sunucusu gelen isteği işleyemez, herhangi bir hata için ambarı-sunucu günlüklerine bakarak daha fazla bilgi edinebilirsiniz. Böyle bir durum buna benzer bir hatadır:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]