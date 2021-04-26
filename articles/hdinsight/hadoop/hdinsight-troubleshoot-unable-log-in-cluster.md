---
title: Azure HDInsight kümesinde oturum açılamıyor
description: Azure HDInsight 'ta neden Apache Hadoop kümesinde oturum geçemedik sorunlarını giderme
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: 03657606f7e24436ff5a851fe7eef1e0c4d5dc24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944302"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Senaryo: Azure HDInsight kümesinde oturum açılamıyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Azure HDInsight kümesinde oturum açılamıyor.

## <a name="cause"></a>Nedeni

Nedenler farklılık gösterebilir. Küme veya uygulama panolarında oturum açarken, "küme oturum açma" veya HTTP kimlik bilgilerinizi kullanmayı unutmayın. Uzaktan bağlanırken Secure Shell (SSH) veya Uzak Masaüstü kimlik bilgilerinizi kullanın.

## <a name="resolution"></a>Çözüm

Sık karşılaşılan sorunları çözmek için aşağıdaki adımlardan birini veya daha fazlasını deneyin.

* Küme panosunu gizlilik modundaki yeni bir tarayıcıda açmayı deneyin.

* SSH kimlik bilgilerinizi hatırlayamıyorsanız, [ambarı Kullanıcı arabirimindeki kimlik bilgilerini sıfırlayabilirsiniz](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]