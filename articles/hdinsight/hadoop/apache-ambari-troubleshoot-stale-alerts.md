---
title: Azure HDInsight 'ta Apache ambarı eski uyarıları
description: HDInsight 'ta Apache ambarı eski uyarıları için olası nedenleri ve çözümleri tartışma ve çözümleme.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 22985f295e504a2d73015f1dc96064f89be47bd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866939"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Senaryo: Azure HDInsight 'ta Apache ambarı eski uyarıları

Bu makalede, Azure HDInsight kümeleriyle etkileşim kurarken sorun giderme adımları ve olası çözümleri açıklanmaktadır.

## <a name="issue"></a>Sorun

Apache ambarı Kullanıcı arabiriminde şöyle bir uyarı görebilirsiniz:

:::image type="content" source="./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png" alt-text="Apache ambarı eski uyarı örneği" border="true":::

## <a name="cause"></a>Nedeni

Ambarı aracıları birçok kaynağın durumunu sürekli olarak izler. *Uyarılar* , belirli küme özelliklerinin önceden belirlenmiş eşiklerin içinde olup olmadığını size bildirecek şekilde yapılandırılabilir. Her kaynak denetimi çalıştıktan sonra, uyarı koşulu karşılanıyorsa, ambarı aracıları durumu Ambarı sunucusuna geri bildirir ve bir uyarı tetikler. Bir uyarı, uyarı profilindeki aralığa göre denetlenmemişse, sunucu bir *ambarı sunucusu eski uyarılar* uyarısını tetikler.

Bir sistem durumu denetiminin tanımlı aralıkta çalıştırılmamasının çeşitli nedenleri vardır:

* Konaklar yoğun kullanım aşamasındadır (yüksek CPU kullanımı), böylece, ambarı aracısının uyarıları zamanında çalıştırmak için yeterli sistem kaynağı alamaz.

* Küme, bir ağır yük sürecinde çok sayıda işi veya hizmeti yürütmekle meşgul.

* Kümedeki az sayıda konak birçok bileşeni barındırır ve bu nedenle birçok uyarıyı çalıştırmak için gereklidir. Bileşen sayısı büyükse, uyarı işleri zamanlanan aralıklarını kaçırmayabilir.

## <a name="resolution"></a>Çözüm

Ambarı eski uyarılarla ilgili sorunları çözmek için aşağıdaki yöntemleri deneyin.

### <a name="increase-the-alert-interval-time"></a>Uyarı aralığı süresini artır

Kümenizin yanıt süresi ve yüküne göre ayrı bir uyarı aralığının değerini artırabilirsiniz:

1. Apache ambarı Kullanıcı arabiriminde, **Uyarılar** sekmesini seçin.
1. İstediğiniz uyarı tanımı adını seçin.
1. Tanımdan **Düzenle**' yi seçin.
1. **Denetim aralığı** değerini artırıp **Kaydet**' i seçin.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Ambarı sunucusu uyarıları için uyarı aralığı süresini artırın

1. Apache ambarı Kullanıcı arabiriminde, **Uyarılar** sekmesini seçin.
1. **Gruplar** açılan listesinden, **ambarı varsayılanı**' nı seçin.
1. **Ambarı sunucusu uyarıları** uyarısını seçin.
1. Tanımdan **Düzenle**' yi seçin.
1. **Denetim aralığı** değerini artırın.
1. **Aralık çarpanı** değerini artırıp **Kaydet**' i seçin.

### <a name="disable-and-reenable-the-alert"></a>Uyarıyı devre dışı bırakma ve yeniden etkinleştirme

Eski bir uyarıyı atmak için devre dışı bırakıp yeniden etkinleştirin:

1. Apache ambarı Kullanıcı arabiriminde, **Uyarılar** sekmesini seçin.
1. İstediğiniz uyarı tanımı adını seçin.
1. Tanımdan, Kullanıcı arabiriminin en sağ bölümünde **etkin** ' i seçin.
1. **Onay** açılır penceresinde **devre dışı bırak**' ı seçin.
1. Sayfada gösterilen tüm uyarı "örneklerinin" temizlenmesi için birkaç saniye bekleyin.
1. Tanımdan, Kullanıcı arabiriminin en sağ bölümünde **devre dışı** seçeneğini belirleyin.
1. **Onay** açılır penceresinde **Etkinleştir**' i seçin.

### <a name="increase-the-alert-grace-period"></a>Uyarı yetkisiz kullanım süresini artırın

Bir ambarı Aracısı, yapılandırılmış bir uyarının zamanlamasını kaçırdığını rapor etmeden önce bir yetkisiz kullanım süresi vardır. Uyarı zamanlanan süresini kaçırırsa ancak yetkisiz kullanım süresi içinde çalıştırıldıysa, eski uyarı oluşturulmaz.

Varsayılan `alert_grace_period` değer 5 saniyedir. Bu ayarı,/etc/Ambari-Agent/conf/ambari-agent.ini ' de yapılandırabilirsiniz. Eski uyarıların düzenli aralıklarla oluştuğu konaklar için değeri 10 ' a artırmayı deneyin. Ardından, ambarı Aracısı 'nı yeniden başlatın.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuz burada bahsedilmiyorsa veya çözemezseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* Azure [Community desteği](https://azure.microsoft.com/support/community/)' nden Azure uzmanlarından yanıt alın.

* Twitter 'da ile bağlantı kurmak [@AzureSupport](https://twitter.com/azuresupport) . Bu, müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabıdır. Azure Community 'yi doğru kaynaklara bağlar: yanıtlar, destek ve uzmanlar.

* Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Buradan yararlanmak için Portal menüsünden Yardım (**?**) seçeneğini belirleyin veya **Yardım + Destek** bölmesini açın. Daha fazla bilgi için bkz. [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md). 

  Abonelik yönetimi ve faturalandırma desteği Microsoft Azure aboneliğinize dahildir. Teknik destek, [Azure destek planları](https://azure.microsoft.com/support/plans/)aracılığıyla sağlanır.