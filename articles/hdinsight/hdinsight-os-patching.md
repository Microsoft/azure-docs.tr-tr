---
title: Azure HDInsight kümeleri için işletim sistemi düzeltme eki uygulama zamanlamasını yapılandırma
description: Linux tabanlı HDInsight kümeleri için işletim sistemi düzeltme eki uygulama zamanlamasını yapılandırmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: 636caf592baa4df771f7cc50095911d0337456d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939394"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux tabanlı HDInsight kümeleri için işletim sistemi düzeltme eki uygulama zamanlamasını yapılandırma

> [!IMPORTANT]
> Ubuntu görüntüleri, yayımlanmakta olan üç ay içinde yeni Azure HDInsight kümesi oluşturma için kullanılabilir hale gelir. Çalışan kümeler otomatik düzeltme eki yok. Müşterilerin çalışan bir kümeyi yaması için betik eylemleri veya diğer mekanizmaları kullanması gerekir. En iyi uygulama olarak, bu betik eylemlerini çalıştırabilir ve küme oluşturulduktan sonra güvenlik güncelleştirmelerini uygulayabilirsiniz.

HDInsight, kümenizde işletim sistemi düzeltme ekleri, güvenlik güncelleştirmeleri ve yeniden başlatma düğümlerini yükleme gibi genel görevleri gerçekleştirmenize yönelik destek sağlar. Bu görevler, [komut dosyası eylemleri](hdinsight-hadoop-customize-cluster-linux.md)olarak çalıştırılabilen ve parametrelerle yapılandırılmış olan aşağıdaki iki betiği kullanarak gerçekleştirilir:

- `schedule-reboots.sh` -Anında yeniden başlatma yapın veya küme düğümlerinde yeniden başlatma zamanlayın.
- `install-updates-schedule-reboots.sh` -Tüm güncelleştirmeleri, yalnızca çekirdek + güvenlik güncelleştirmelerini veya yalnızca çekirdek güncelleştirmelerini yükler.

> [!NOTE]  
> Betik eylemleri, gelecekteki tüm güncelleştirme döngüleri için güncelleştirmeleri otomatik olarak uygulamaz. Güncelleştirmeleri yüklemek için yeni güncelleştirmelerin uygulanması gereken her seferinde komut dosyalarını çalıştırın ve ardından sanal makineyi yeniden başlatın.

## <a name="preparation"></a>Hazırlık

Üretime dağıtmadan önce temsilcinin üretim dışı bir ortamda yama yapın. Gerçek düzeltme eki uygulamanıza başlamadan sisteminizi yeterince test etmek için bir plan geliştirin.

Kümenizin bulunduğu bir SSH oturumundan zaman, güvenlik güncelleştirmelerinin kullanılabilir olduğunu belirten bir ileti alabilirsiniz. İleti şöyle görünebilir:

```
89 packages can be updated.
82 updates are security updates.

*** System restart required ***

Welcome to Spark on HDInsight.

```

Düzeltme eki uygulama isteğe bağlıdır.

## <a name="restart-nodes"></a>Yeniden başlatma düğümleri
  
Betik [zamanlaması-yeniden başlatılır](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh), kümedeki makinelerde gerçekleştirilecek yeniden başlatma türünü ayarlar. Betik eylemi gönderilirken, bu üç düğüm türüne uygulanacak şekilde ayarlayın: baş düğüm, çalışan düğümü ve Zookeeper. Betik bir düğüm türüne uygulanmadıysa, bu düğüm türü için VM 'Ler güncellenmez veya yeniden başlatılmaz.

`schedule-reboots script`Tek bir sayısal parametreyi kabul eder:

| Parametre | Kabul edilen değerler | Tanım |
| --- | --- | --- |
| Gerçekleştirilecek yeniden başlatma türü | 1 veya 2 | 1 değeri, zamanlamayı yeniden başlatmayı (12-24 saat içinde zamanlanmış) mümkün. 2 değeri hemen yeniden başlatmaya (5 dakika içinde) izin vermez. Hiçbir parametre verilmezse varsayılan değer 1 ' dir. |  

## <a name="install-updates-and-restart-nodes"></a>Güncelleştirmeleri yükler ve düğümleri yeniden başlatın

Betik [install-Updates-Schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) farklı güncelleştirme türlerini yüklemek ve VM 'yi yeniden başlatmak için seçenekler sağlar.

`install-updates-schedule-reboots`Komut dosyası, aşağıdaki tabloda açıklandığı gibi iki sayısal parametreyi kabul eder:

| Parametre | Kabul edilen değerler | Tanım |
| --- | --- | --- |
| Yüklenecek güncelleştirmelerin türü | 0, 1 veya 2 | 0 değeri yalnızca çekirdek güncelleştirmelerini yüklüyor. 1 değeri çekirdek + güvenlik güncelleştirmelerini ve 2 tüm güncelleştirmeleri yüklüyor. Hiçbir parametre sağlanmazsa varsayılan değer 0 ' dır. |
| Gerçekleştirilecek yeniden başlatma türü | 0, 1 veya 2 | 0 değeri yeniden başlatmayı devre dışı bırakır. 1 değeri, zamanlamayı yeniden başlatmaya izin verebilir ve 2 hemen yeniden başlatmaya izin vermez. Hiçbir parametre sağlanmazsa varsayılan değer 0 ' dır. Kullanıcı giriş parametresi 1 ' i giriş parametresi 2 ' ye değiştirmesi gerekir. |

> [!NOTE]
> Mevcut bir kümeye uyguladıktan sonra bir betiği kalıcı olarak işaretlemeniz gerekir. Aksi takdirde, ölçeklendirme işlemleri aracılığıyla oluşturulan tüm yeni düğümler varsayılan düzeltme eki uygulama zamanlamasını kullanır. Betiği küme oluşturma işleminin parçası olarak uygularsanız, otomatik olarak kalıcı hale getirilir.

> [!NOTE]
> Zamanlanmış yeniden başlatma seçeneği, düzeltme eki uygulanmış küme düğümlerinin 12 ile 24 saat arasında bir süre içinde otomatik olarak yeniden başlatılmasını ve yüksek kullanılabilirlik, güncelleştirme etki alanı ve hata etki alanı konularını dikkate alır. Zamanlanmış yeniden başlatma, çalışan iş yüklerini sonlandırır, ancak düğümler kullanılamadığında, daha uzun süre işlem süreleriyle küme kapasitesini dışarıda bırakabilir. 

## <a name="next-steps"></a>Sonraki adımlar

Betik eylemlerini kullanmayla ilgili belirli adımlar için, [betik eylemi kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md)bölümünde yer alan aşağıdaki bölümlere bakın:

- [Küme oluşturma sırasında betik eylemi kullanma](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Çalışan bir kümeye betik eylemi uygulama](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
