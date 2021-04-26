---
title: İzleme hatası Azure HDInsight kümesinden hata durumunda yumuşak kilit hatası
description: İzleme hatası Soft kilitleniyor CPU, Azure HDInsight kümesi 'ndeki çekirdek Syslog 'lar 'da görünür
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 5d9d7b0fc21660dd22ff92bbe2de38c759c440ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944340"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Senaryo: "izleme: hata: bir Azure HDInsight kümesinden yazılım kilitleme-CPU" hatası

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Çekirdek Syslog 'lar hata iletisini içerir: `watchdog: BUG: soft lockup - CPU` .

## <a name="cause"></a>Nedeni

Linux çekirdeğindeki bir [hata](https://bugzilla.kernel.org/show_bug.cgi?id=199437) , CPU yumuşak lockups sorunlarına neden oluyor.

## <a name="resolution"></a>Çözüm

Çekirdek Düzeltme Eki uygulayın. Aşağıdaki betik, Linux çekirdeğini yükseltir ve makineleri 24 saat üzerinden farklı zamanlarda yeniden başlatır. Betik eylemini iki toplu işlem halinde yürütün. İlk Batch, baş düğüm hariç tüm düğümlerde bulunur. İkinci toplu işlem baş düğümüdür. Baş düğüm ve diğer düğümleri aynı anda çalıştırmayın.

1. Azure portal HDInsight kümenize gidin.

1. Betik eylemlerine gidin.

1. **Yeni Gönder** ' i seçin ve girişi aşağıdaki gibi girin

    | Özellik | Değer |
    | --- | --- |
    | Betik türü | -Özel |
    | Name |Çekirdek geçici kilit sorunu için çözüm |
    | Bash betiği URI 'SI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Düğüm türleri |Çalışan, Zookeeper |
    | Parametreler |Yok |

    Yeni düğümler eklendiğinde betiği yürütmek istiyorsanız **bu betiği kalıcı yap...** öğesini seçin.

1. **Oluştur**’u seçin.

1. Yürütmenin başarılı olmasını bekleyin.

1. Adım 3 ile aynı adımları izleyerek (Bu kez düğüm türleri: Head) betik eylemini baş düğümde yürütün.

1. Yürütmenin başarılı olmasını bekleyin.

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]