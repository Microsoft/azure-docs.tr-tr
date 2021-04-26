---
title: Azure portal sanal makine ölçek kümelerini otomatik ölçeklendirme
description: Azure portal sanal makine ölçek kümeleri için otomatik ölçeklendirme kuralları oluşturma
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 2ee2db62cf43dc191da2b92f7d4b67ff775f628f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537529"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Azure portal bir sanal makine ölçek kümesini otomatik olarak ölçeklendirme
Ölçek kümesi oluşturduğunuzda, çalıştırmak istediğiniz VM örneği sayısını tanımlarsınız. Uygulamanızın talebi değiştikçe, sanal makine örneklerinin sayısını otomatik olarak artırabilir veya azaltabilirsiniz. Otomatik ölçeklendirme özelliği, uygulamanızın yaşam döngüsü boyunca uygulama performansındaki değişikliklere veya müşteri taleplerine ayak uydurmanıza olanak tanır.

Bu makalede, ölçek kümesindeki sanal makine örneklerinin performansını izleyen Azure portal otomatik ölçeklendirme kuralları oluşturma konusu gösterilmektedir. Bu otomatik ölçeklendirme kuralları, bu performans ölçümlerine yanıt olarak sanal makine örneklerinin sayısını artırır veya azaltır. Bu adımları [Azure PowerShell](tutorial-autoscale-powershell.md) veya [Azure CLI](tutorial-autoscale-cli.md)ile de tamamlayabilirsiniz.


## <a name="prerequisites"></a>Önkoşullar
Otomatik ölçeklendirme kuralları oluşturmak için, var olan bir sanal makine ölçek kümesine ihtiyacınız vardır. [Azure Portal](quick-create-portal.md), [Azure POWERSHELL](quick-create-powershell.md)veya [Azure CLI](quick-create-cli.md)ile bir ölçek kümesi oluşturabilirsiniz.


## <a name="create-a-rule-to-automatically-scale-out"></a>Otomatik olarak genişletmek için bir kural oluşturun
Uygulamanızın talebi artarsa, ölçek kümenizdeki sanal makine örneklerinde üzerindeki yük de artar. Bu kısa süreli bir talep olmayıp tutarlı şekilde yük artıyorsa, ölçek kümesindeki sanal makine örneği sayısını artırmak için otomatik ölçeklendirme kuralları yapılandırabilirsiniz. Bu sanal makine örnekleri oluşturulduğunda ve uygulamalarınız dağıtıldığında ölçek kümesi, yük dengeleyici aracılığıyla bunlara trafiği dağıtmaya başlar. CPU veya disk gibi hangi ölçümlerin izleneceğini, uygulama yükünün belirli bir eşiği ne kadar süre karşılaması gerektiği ve ölçek kümesine kaç tane sanal makine örneği ekleneceğini denetlersiniz.

1. Azure portal açın ve panonun sol tarafındaki menüden **kaynak grupları** ' nı seçin.
2. Ölçek kümesini içeren kaynak grubunu seçin ve ardından kaynak listesinden ölçek kümesini seçin.
3. Ölçek kümesi penceresinin sol tarafındaki menüden **ölçeklendirin** ' ı seçin. **Otomatik ölçeklendirmeyi etkinleştirmek** için düğmeyi seçin:

    ![Azure portal otomatik ölçeklendirmeyi etkinleştir](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Ayarlarınıza *Otomatik ölçeklendirme* gibi bir ad girin, sonra **kural ekleme** seçeneğini belirleyin.

5. Ortalama CPU yükü 10 dakikalık bir dönemde %70 ' den büyük olduğunda bir ölçek kümesindeki sanal makine örneği sayısını artıran bir kural oluşturalım. Kural tetiklendiğinde, sanal makine örneklerinin sayısı %20 oranında artırılır. Az sayıda sanal makine örneği olan ölçek kümelerinde, **işlemi** *sayı ile artırmak* Için ayarlayabilir ve ardından *örnek sayısı* olarak *1* veya *2* değerini belirtebilirsiniz. Çok sayıda VM örneğiyle ölçek kümelerinde %10 veya %20 sanal makine örneği daha uygun olabilir.

    Kuralınız için aşağıdaki ayarları belirtin:
    
    | Parametre              | Açıklama                                                                                                         | Değer          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Zaman toplama*     | Toplanan ölçümlerin analiz için nasıl bir araya getirileceğini tanımlar.                                                | Ortalama        |
    | *Ölçüm adı*          | İzlenecek ve ölçek kümesi eylemlerinin uygulanmasında temel alınacak performans ölçümü.                                                   | CPU yüzdesi |
    | *Zaman dilimi istatistiği* | Her seferinde toplanan ölçümlerin analiz için nasıl toplanması gerektiğini tanımlar.                             | Ortalama        |
    | *İşleç*             | Ölçüm verilerini eşikle karşılaştırmak için kullanılan işleç.                                                     | Büyüktür   |
    | *Eşik*            | Otomatik ölçeklendirme kuralının bir eylemi tetiklemesine neden olan yüzde.                                                 | 70             |
    | *Süre*             | Ölçüm ve eşik değerleri karşılaştırılmadan önce izlenecek süre. Cool azaltma süresini içermez.                                   | 10 dakika     |
    | *İşlem*            | Kural geçerli olduğunda ölçek kümesinin ölçeği büyütme veya küçültme yapmanız gerektiğini tanımlar.                        | Yüzdeyi yüzde artır |
    | *Örnek sayısı*       | Kural tetiklendiğinde değiştirilmesi gereken sanal makine örneklerinin yüzdesi.                                            | 20             |
    | *Soğuma (dakika)*  | Otomatik ölçeklendirme eylemlerinin geçerli olması için kural tekrar uygulanmadan önceki bekleme süresi. | 5 dakika      |

    Aşağıdaki örneklerde, bu ayarlarla eşleşen Azure portal oluşturulan bir kural gösterilmektedir:

    ![Sanal makine örneği sayısını artırmak için bir otomatik ölçeklendirme kuralı oluşturma](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Kuralı oluşturmak için **Ekle** ' yi seçin.


## <a name="create-a-rule-to-automatically-scale-in"></a>Otomatik olarak ölçeklendirmek için bir kural oluşturun
Bir akşam veya hafta sonu uygulama talebiniz azalabilir. Yük belirli bir süreye yayılarak tutarlı şekilde azalıyorsa, ölçek kümesindeki sanal makine örneği sayısını azaltmak için otomatik ölçeklendirme kuralları yapılandırabilirsiniz. Mevcut talebi karşılamak için gerekli örnek sayısını yalnızca siz çalıştırdığınızdan, bu ölçeği daraltma eylemi ölçek kümenizi çalıştırma maliyetini azaltır.

1. **Bir kuralı yeniden eklemeyi** seçin.
2. Ortalama CPU yükü, 10 dakikalık bir dönem boyunca %30 ' un altına düştüğünde bir ölçek kümesindeki sanal makine örneklerinin sayısını azaltan bir kural oluşturun. Kural tetiklendiğinde, sanal makine örneklerinin sayısı %20 oranında azaltılır.

    Önceki kuralla aynı yaklaşımı kullanın. Kuralınız için aşağıdaki ayarları yapın:
    
    | Parametre              | Açıklama                                                                                                          | Değer          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operatör*             | Ölçüm verilerini eşikle karşılaştırmak için kullanılan işleç.                                                      | Küçüktür   |
    | *Eşik*            | Otomatik ölçeklendirme kuralının bir eylemi tetiklemesine neden olan yüzde.                                                 | 30             |
    | *İşlem*            | Kural geçerli olduğunda ölçek kümesinin ölçeği büyütme veya küçültme yapmanız gerekip gerekmediğini tanımlar                         | Yüzdeyi azalt |
    | *Örnek sayısı*       | Kural tetiklendiğinde değiştirilmesi gereken sanal makine örneklerinin yüzdesi.                                             | 20             |

3. Kuralı oluşturmak için **Ekle** ' yi seçin.


## <a name="define-autoscale-instance-limits"></a>Otomatik ölçeklendirme örneği sınırlarını tanımla
Otomatik ölçeklendirme profiliniz en düşük, en yüksek ve varsayılan VM örneği sayısını tanımlamalıdır. Otomatik ölçeklendirme kurallarınız uygulandığında, bu örnek sınırları en fazla örnek sayısını ölçeklendirdiğinizden veya minimum örnek sayısının ötesinde ölçeklendirdiğinizden emin olun.

1. Aşağıdaki örnek sınırlarını ayarlayın:

    | Minimum | Maksimum | Varsayılan|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Otomatik ölçeklendirme kurallarınızı ve örnek sınırlarınızı uygulamak için **Kaydet**' i seçin.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Ölçek kümesindeki örneklerin sayısını izleme
VM örneklerinin sayısını ve durumunu görmek için ölçek kümesi penceresinin sol tarafındaki menüden **örnekler** ' i seçin. Durum, ölçek kümesi otomatik olarak ölçeklendirilirken veya ölçeklendirilirken ölçek otomatik olarak ölçeklendirilirken *, sanal* makine örneğinin *oluşturuyor* olduğunu gösterir.

![Ölçek kümesi sanal makine örneklerinin listesini görüntüleyin](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Zamanlamaya göre otomatik ölçeklendirme
Önceki örnekler, CPU kullanımı gibi temel ana bilgisayar ölçümleri ile, veya dışında bir ölçek kümesini otomatik olarak ölçeklendirin. Ayrıca, zamanlamaları temel alan otomatik ölçeklendirme kuralları da oluşturabilirsiniz. Bu zamanlama tabanlı kurallar, çekirdek çalışma saatleri gibi uygulama talebindeki beklenen artışdan önce sanal makine örneklerinin sayısını otomatik olarak ölçeklendirmenize olanak tanır ve ardından, hafta sonu gibi daha az talebi öngören bir seferde örnek sayısında otomatik olarak ölçeklendirebilirsiniz.

1. Ölçek kümesi penceresinin sol tarafındaki menüden **ölçeklendirin** ' ı seçin. Önceki örneklerde oluşturulan mevcut otomatik ölçeklendirme kurallarını silmek için çöp kutusu simgesini seçin.

    ![Varolan otomatik ölçeklendirme kurallarını Sil](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. **Ölçek koşulu eklemeyi** seçin. Kural adı ' nın yanındaki kurşun kalem simgesini seçin ve *her iş gününde ölçeği* genişletme gibi bir ad sağlayın.

    ![Varsayılan otomatik ölçeklendirme kuralını yeniden adlandır](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. **Belirli bir örnek sayısına ölçeklendirmek** için radyo düğmesini seçin.
4. Örnek sayısını ölçeklendirmek için örnek sayısı olarak *10* girin.
5. Zamanlama türü için **belirli günleri Yinele** ' **yi** seçin.
6. Pazartesi 'den Cuma 'Ya kadar tüm iş günlerini seçin.
7. Uygun saat dilimini seçin ve ardından *09:00* **başlangıç saatini** belirtin.
8. **Bir ölçek koşulu eklemeyi** tekrar seçin. *3* örneğe ölçeklendirerek, haftanın her günü tekrarlandığı ve *18:00*' *de başlayan akşam içinde ölçek* adlı bir zamanlama oluşturmak için işlemi tekrarlayın.
9. Zamanlama tabanlı otomatik ölçeklendirme kurallarınızı uygulamak için **Kaydet**' i seçin.

    ![Bir zamanlamaya göre ölçeklenen otomatik ölçeklendirme kuralları oluşturma](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Otomatik ölçeklendirme kurallarınızın nasıl uygulandığını görmek için **ölçekleme** penceresinin en üstündeki **geçmişi Çalıştır** ' ı seçin. Grafik ve olay listesi, otomatik ölçeklendirme kurallarının tetiklenme ve ölçek kümesindeki sanal makine örneklerinin sayısı arttıkça veya azaldıkça görüntülenir.


## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, ölçek kümesindeki sanal makine örneklerinin *sayısını* yatay olarak ölçeklendirmek ve azaltmak için otomatik ölçeklendirme kurallarını nasıl kullanacağınızı öğrendiniz. Ayrıca, sanal makine örnek *boyutunu* artırmak veya azaltmak için dikey olarak ölçeklendirebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makine Ölçek Kümeleri Ile dikey otomatik ölçeklendirme](virtual-machine-scale-sets-vertical-scale-reprovision.md).

VM örneklerinizi yönetme hakkında daha fazla bilgi için bkz. [Azure PowerShell ile sanal makine ölçek kümelerini yönetme](./virtual-machine-scale-sets-manage-powershell.md).

Otomatik ölçeklendirme kurallarınızın tetiklenmesi durumunda uyarı oluşturma hakkında bilgi edinmek için bkz. [Azure izleyici 'de e-posta ve Web kancası uyarı bildirimleri göndermek için otomatik ölçeklendirme eylemlerini kullanma](../azure-monitor/autoscale/autoscale-webhook-email.md). [Azure izleyici 'de e-posta ve Web kancası uyarı bildirimleri göndermek için Denetim günlüklerini de kullanabilirsiniz](../azure-monitor/alerts/alerts-log-webhook.md).
