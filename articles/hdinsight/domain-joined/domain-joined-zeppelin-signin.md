---
title: Azure HDInsight 'ta Zeppelin 'da oturum açılamıyor
description: Azure HDInsight 'ta Zeppelin 'da oturum açılamıyor
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: d4bb9e090b238eacec77f4c19bbf9afb3e09a912
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091056"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta Apache Zeppelin 'de oturum açılamıyor

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Active Directory 'de parola ekleme parolası değiştirildikten sonra Apache Zeppelin 'da oturum açılamıyor.

## <a name="cause"></a>Nedeni

Dosyasında`shiro_ini` bahsedilen `activeDirectoryRealm.systemUsername` Kullanıcı Active Directory parolasını değiştirdi.

## <a name="resolution"></a>Çözüm

1. Değiştirilen parolanın, ambarı 'nda Zeppelin `activeDirectoryRealm.systemPassword = <new password>` `shiro_ini` config dosyasına dahil olan kök nedeni olduğunu doğrulayın. `activeDirectoryRealm.hadoopSecurityCredentialPath` Ayarı kaldırın. Aşağıdaki konumudur `shiro ini`.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Kullanıcılar artık 1. adımdan sonra Zeppelin 'da oturum açabilirler, yeni parolayla yeni `jceks` bir dosya oluşturun ve yeni dosyayla `activeDirectoryRealm.hadoopSecurityCredentialPath` değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

* [@AzureSupport](https://twitter.com/azuresupport) Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabına bağlanın. Azure Community 'yi doğru kaynaklara bağlama: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Abonelik yönetimi ve faturalandırma desteği 'ne erişim Microsoft Azure aboneliğinize dahildir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
