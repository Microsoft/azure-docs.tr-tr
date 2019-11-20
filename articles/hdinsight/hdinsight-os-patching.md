---
title: Azure HDInsight kümeleri için işletim sistemi düzeltme eki uygulama zamanlamasını yapılandırma
description: Linux tabanlı HDInsight kümeleri için işletim sistemi düzeltme eki uygulama zamanlamasını yapılandırmayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a97a03f7ef20ae56cec04341fe76b79ee657547b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748483"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux tabanlı HDInsight kümeleri için işletim sistemi düzeltme eki uygulama zamanlamasını yapılandırma

> [!IMPORTANT]
> Ubuntu görüntüleri, yayımlanmakta olan üç ay içinde yeni Azure HDInsight kümesi oluşturma için kullanılabilir hale gelir. 2019 Ocak itibariyle çalıştırılan kümeler otomatik düzeltme eki olmaz. Müşterilerin çalışan bir kümeyi yaması için betik eylemleri veya diğer mekanizmaları kullanması gerekir. Yeni oluşturulan kümeler en son güvenlik düzeltme ekleri dahil olmak üzere her zaman en son kullanılabilir güncelleştirmelere sahip olur.

HDInsight, kümenizde işletim sistemi düzeltme ekleri, güvenlik güncelleştirmeleri ve yeniden başlatma düğümlerini yükleme gibi genel görevleri gerçekleştirmenize yönelik destek sağlar. Bu görevler, [komut dosyası eylemleri](hdinsight-hadoop-customize-cluster-linux.md)olarak çalıştırılabilen ve parametrelerle yapılandırılmış olan aşağıdaki iki betiği kullanarak gerçekleştirilir:

- `schedule-reboots.sh`-hemen yeniden başlatma yapın veya küme düğümlerinde yeniden başlatma zamanlayın.
- `install-updates-schedule-reboots.sh`-tüm güncelleştirmeleri, yalnızca çekirdek + güvenlik güncelleştirmelerini veya yalnızca çekirdek güncelleştirmelerini yükler.

> [!NOTE]  
> Betik eylemleri, gelecekteki tüm güncelleştirme döngüleri için güncelleştirmeleri otomatik olarak uygulamaz. Güncelleştirmeleri yüklemek için yeni güncelleştirmelerin uygulanması gereken her seferinde komut dosyalarını çalıştırın ve ardından sanal makineyi yeniden başlatın.

## <a name="restart-nodes"></a>Yeniden başlatma düğümleri
  
Betik [zamanlaması-yeniden başlatılır](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh), kümedeki makinelerde gerçekleştirilecek yeniden başlatma türünü ayarlar. Betik eylemi gönderilirken, bu üç düğüm türüne uygulanacak şekilde ayarlayın: baş düğüm, çalışan düğümü ve Zookeeper. Betik bir düğüm türüne uygulanmadıysa, bu düğüm türü için VM 'Ler güncellenmez veya yeniden başlatılmaz.

`schedule-reboots script` bir sayısal parametreyi kabul eder:

| Parametre | Kabul edilen değerler | Tanım |
| --- | --- | --- |
| Gerçekleştirilecek yeniden başlatma türü | 1 veya 2 | 1 değeri, zamanlamayı yeniden başlatmayı (12-24 saat içinde zamanlanmış) mümkün. 2 değeri hemen yeniden başlatmaya (5 dakika içinde) izin vermez. Hiçbir parametre verilmezse varsayılan değer 1 ' dir. |  

## <a name="install-updates-and-restart-nodes"></a>Güncelleştirmeleri yükler ve düğümleri yeniden başlatın

Betik [install-Updates-Schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) farklı güncelleştirme türlerini yüklemek ve VM 'yi yeniden başlatmak için seçenekler sağlar.

`install-updates-schedule-reboots` betiği, aşağıdaki tabloda açıklandığı gibi iki sayısal parametreyi kabul eder:

| Parametre | Kabul edilen değerler | Tanım |
| --- | --- | --- |
| Yüklenecek güncelleştirmelerin türü | 0, 1 veya 2 | 0 değeri yalnızca çekirdek güncelleştirmelerini yüklüyor. 1 değeri tüm güncelleştirmeleri yüklüyor ve 2 yalnızca çekirdek + güvenlik güncelleştirmelerini yüklüyor. Hiçbir parametre sağlanmazsa varsayılan değer 0 ' dır. |
| Gerçekleştirilecek yeniden başlatma türü | 0, 1 veya 2 | 0 değeri yeniden başlatmayı devre dışı bırakır. 1 değeri, zamanlamayı yeniden başlatmaya izin verebilir ve 2 hemen yeniden başlatmaya izin vermez. Hiçbir parametre sağlanmazsa varsayılan değer 0 ' dır. Kullanıcı giriş parametresi 1 ' i giriş parametresi 2 ' ye değiştirmesi gerekir. |

> [!NOTE]
> Mevcut bir kümeye uyguladıktan sonra bir betiği kalıcı olarak işaretlemeniz gerekir. Aksi takdirde, ölçeklendirme işlemleri aracılığıyla oluşturulan tüm yeni düğümler varsayılan düzeltme eki uygulama zamanlamasını kullanır. Betiği küme oluşturma işleminin parçası olarak uygularsanız, otomatik olarak kalıcı hale getirilir.

## <a name="next-steps"></a>Sonraki adımlar

Betik eylemlerini kullanmayla ilgili belirli adımlar için, [betik eylemi kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md)bölümünde yer alan aşağıdaki bölümlere bakın:

* [Küme oluşturma sırasında betik eylemi kullanma](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Çalışan bir kümeye betik eylemi uygulama](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
