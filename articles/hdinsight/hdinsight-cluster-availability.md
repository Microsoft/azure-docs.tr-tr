---
title: 'İzleme: Apache ambarı & Azure Izleyici günlükleri-Azure HDInsight'
description: Küme durumunu ve kullanılabilirliğini izlemek için ambarı ve Azure Izleyici günlüklerini nasıl kullanacağınızı öğrenin.
keywords: izleme, ambarı, izleyici, Log Analytics, uyarı, kullanılabilirlik, sistem durumu
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.openlocfilehash: eeaef8851035bbb8d2f39bcf9f366118545fcf0f
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044473"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Ambarı ve Azure Izleyici günlükleri ile küme kullanılabilirliğini izleme

HDInsight kümeleri hem bir bakışta, hem de önceden tanımlanmış uyarılarda sağlık bilgileri sağlayan ve ayrıca, sorgulanabilir ölçümler ve Günlükler ve yapılandırılabilir uyarılar sağlayan Azure Izleyici günlükleri tümleştirmesinin yanı sıra hem Apache ambarı içerir.

Bu belge, kümenizi izlemek ve bir veya daha fazla düğümden bir sinyal alınmadığında harekete geçen bir Azure Izleyici uyarısı oluşturmak için bu araçların nasıl kullanılacağını gösterir. beş saat içinde.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Pano

**Ambarı panosuna** , aşağıda gösterildiği gibi, HDInsight genel bakış dikey penceresinin **küme panoları** bölümünde yer alarak, aşağıdaki gibi Azure Portal. Alternatif olarak, aşağıdaki URL bir Browser [https://\<clustername\>girerek erişilebilir. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![HDInsight kaynak portalı görünümü](media/hdinsight-cluster-availability/portal-oms-overview1.png)

Ardından, bir küme oturum açma Kullanıcı adı ve parolası istenir. Kümeyi oluştururken seçtiğiniz kimlik bilgilerini girin.

Daha sonra, HDInsight kümenizin sistem durumuna hızlı bir genel bakış sağlayan bir dizi ölçümü gösteren pencere öğeleri içeren bir işlem tablosu ' na yönlendirilirsiniz. Bu pencere öğeleri, Spark ve Hadoop kümeleri için YARN ResourceManager çalışma süresi gibi belirli küme türlerine özgü ölçümler ve canlı düğüm sayısı (çalışan düğümleri) ve JournalNodes (Zookeeper node), Üçlü iş çalışma süresi (örneğin, çok sayıda) gibi ölçümleri gösterir.

![Apache ambarı kullanım panosu görüntüleme](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Konaklar – tek tek düğüm durumunu görüntüle

Tek tek düğümlerin durum bilgilerini de görüntüleyebilirsiniz. Kümenizdeki tüm düğümlerin listesini görüntülemek için **konaklar** sekmesine tıklayın ve her düğüm hakkında temel bilgileri görüntüleyin. Her düğüm adının solunda yeşil onay işareti, tüm bileşenlerin düğüm üzerinde olduğunu gösterir. Bir bileşen düğüm üzerinde kapalıysa yeşil onay yerine kırmızı bir uyarı üçgeni görürsünüz.

![HDInsight Apache ambarı Konakları görünümü](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

Daha sonra, söz konusu düğümün daha ayrıntılı konak ölçümlerini görüntülemek için bir düğümün **adına** tıklayabilirsiniz. Bu görünüm her bir bileşenin durumunu/kullanılabilirliğini gösterir.

![Apache ambarı tek düğümlü görünüm barındırır](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambarı uyarıları

Ayrıca, belirli olaylara yönelik bildirim sağlayabilen birkaç yapılandırılabilir uyarı da sunar. Uyarılar tetiklendiğinde, bu uyarılar, bu uyarı sayısını içeren kırmızı bir rozet içinde, ambarı 'nın sol üst köşesinde gösterilir. Bu rozet öğesine tıkladığınızda geçerli uyarıların bir listesi gösterilir.

![Apache ambarı geçerli uyarı sayısı](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Uyarı tanımlarının ve durumlarının listesini görüntülemek için, aşağıda gösterildiği gibi **Uyarılar** sekmesine tıklayın.

![Ambarı uyarı tanımları görünümü](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambarı aşağıdakiler dahil olmak üzere kullanılabilirliğiyle ilgili çok sayıda önceden tanımlı uyarı sunar:

| Uyarı adı                        | Açıklama                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dadtanode sistem durumu Özeti           | Sağlıksız bir kades varsa, bu hizmet düzeyi uyarı tetiklenir                                                                                                                |
| Süs Yot yüksek kullanılabilirlik durumu | Bu hizmet düzeyi uyarı, etkin bir süs Code veya standby süs ODE çalışmıyorsa tetiklenir.                                                                              |
| Kullanılabilir JournalNodes yüzdesi    | Bu uyarı, kümedeki aşağı yönelik JournalNodes sayısı yapılandırılan kritik eşikten fazlaysa tetiklenir. Bu, JournalNode işlem denetimlerinin sonuçlarını toplar. |
| Kullanılabilir gün yüzdesi       | Bu uyarı, kümedeki aşağı doğru eşik sayısı yapılandırılan kritik eşikten büyükse tetiklenir. Bu, Davtanode işlem denetimlerinin sonuçlarını toplar.       |

Bir kümenin kullanılabilirliğini izlemeye yardımcı olan bir dizi uyarı listesinin tam listesi [burada](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)bulunabilir.

Bir uyarının veya değişiklik ölçütlerinin ayrıntılarını görüntülemek için uyarının **adına** tıklayın. Örnek olarak, **Davode sistem durumu özetini** alın. Bir ' uyarı ' veya ' kritik ' uyarı ve kriterlerin denetim aralığı tetiklenecek belirli ölçütlere ek olarak uyarının açıklamasını görebilirsiniz. Yapılandırmayı düzenlemek için yapılandırma kutusunun sağ üst köşesindeki **Düzenle** düğmesine tıklayın.

![Apache ambarı uyarı yapılandırması](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Burada, açıklamayı ve uyarı ya da kritik uyarılar için denetim aralığını ve eşikleri düzenleyebilirsiniz.

![Ambarı uyarı yapılandırması düzenleme görünümü](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

Bu örnekte, önemli bir uyarı tetikleyebilmeniz ve 1 sağlıksız bir ıtanode yalnızca uyarı tetikleyebilmeniz gerekir. Düzenlemeden sonra **Kaydet** ' e tıklayın.

### <a name="email-notifications"></a>E-posta bildirimleri

Ayrıca, isteğe bağlı olarak, ambarı uyarıları için e-posta bildirimleri yapılandırabilirsiniz. Bunu yapmak için, **Uyarılar** sekmesinde, sol üst köşedeki **Eylemler** düğmesine ve ardından **Bildirimleri Yönet** ' e tıklayın.

![Ambarı yönetimi bildirimleri eylemi](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Uyarı bildirimlerini yönetmek için bir iletişim kutusu açılır. İletişim kutusunun alt kısmındaki **+** tıklayın ve e-postaların gönderileceği e-posta sunucusu ayrıntılarını kullanarak ambarı sağlamak için gerekli alanları doldurun.

> [!TIP]
> Ambarı e-posta bildirimlerinin kurulması, birçok HDInsight kümesini yönetirken tek bir yerde uyarı almanın iyi bir yoludur.

## <a name="azure-monitor-logs-integration"></a>Azure Izleyici günlük tümleştirmesi

Azure Izleyici günlükleri, HDInsight kümeleri gibi birden çok kaynak tarafından oluşturulan verilerin, birleştirilmiş bir izleme deneyimi elde etmek için tek bir yerde toplanmasını ve toplanmasını sağlar.

Bir önkoşul olarak, toplanan verileri depolamak için bir Log Analytics çalışma alanına ihtiyacınız olacaktır. Henüz bir tane oluşturmadıysanız, buradaki yönergeleri izleyebilirsiniz: [Log Analytics çalışma alanı oluşturun](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HDInsight Azure Izleyici günlükleri tümleştirmesini etkinleştirme

Portaldaki HDInsight küme kaynağı sayfasından **Operations Management Suite** dikey penceresine tıklayın. Ardından, **Etkinleştir** ' e tıklayın ve açılan listeden Log Analytics çalışma alanınızı seçin.

![HDInsight Operations Management Suite dikey penceresi](media/hdinsight-cluster-availability/hdi-portal-oms-enable.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Günlükler dikey penceresinde ölçümleri ve günlük tablolarını sorgulama

Azure Izleyici günlük tümleştirmesi etkinleştirildikten sonra (Bu işlem birkaç dakika sürebilir) **Log Analytics çalışma alanı** kaynağına gidin ve **Günlükler** dikey penceresine tıklayın

![Log Analytics çalışma alanı günlükleri dikey penceresi](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

**Günlükler** dikey penceresinde, şöyle bir dizi örnek sorgu listelenir:

| Sorgu adı                      | Açıklama                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Günümüzde kullanılabilirlik bilgisayarları    | Günlük gönderen bilgisayarların sayısını, her saat                     |
| Sinyalleri Listele                 | Son saatin tüm bilgisayar sinyalleriyle listeleme                           |
| Her bilgisayarın son sinyali | Her bilgisayar tarafından gönderilen son sinyali gösterme                             |
| Kullanılamayan bilgisayarlar           | Son 5 saat içinde sinyal göndermediği bilinen tüm bilgisayarları Listele |
| Kullanılabilirlik oranı               | Bağlı her bilgisayarın kullanılabilirlik oranını hesapla                |

Örnek olarak, yukarıdaki ekran görüntüsünde gösterildiği gibi, bu sorguda **Çalıştır** ' a tıklayarak **kullanılabilirlik oranı** örnek sorgusunu çalıştırın. Bu, kümenizde her bir düğümün kullanılabilirlik oranını yüzde olarak gösterir. Aynı Log Analytics çalışma alanına ölçümleri göndermek için birden çok HDInsight kümesi etkinleştirdiyseniz, bu kümelerdeki tüm düğümlerin kullanılabilirlik oranını görürsünüz.

![Log Analytics çalışma alanı günlükleri dikey penceresi ' kullanılabilirlik oranı ' örnek sorgu](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Kullanılabilirlik oranı, 24 saatlik bir dönemde ölçülür, bu sayede doğru kullanılabilirlik ücretleri görüntülenmeden önce kümenizin en az 24 saat boyunca çalışması gerekir.

Sağ üst köşedeki **sabitle** ' ye tıklayarak bu tabloyu paylaşılan bir panoya sabitleyebilirsiniz. Yazılabilir bir paylaşılan panonuz yoksa, nasıl bir tane oluşturabileceğiniz hakkında bilgi edinebilirsiniz: [Azure Portal panoları oluşturma ve paylaşma](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Azure Izleyici uyarıları

Ayrıca, bir ölçüm değeri veya bir sorgu sonuçlarının belirli koşullara uyması durumunda tetiklenecek Azure Izleyici uyarılarını da ayarlayabilirsiniz. Örnek olarak, bir veya daha fazla düğüm 5 saat içinde bir sinyal göndermediği zaman bir e-posta göndermek için bir uyarı oluşturalım (yani, kullanılamaz olarak kabul edilir).

**Günlükler** dikey penceresinde, aşağıda gösterildiği gibi, bu sorguda **Çalıştır** ' a tıklayarak **kullanılamayan bilgisayarlar** örnek sorgusunu çalıştırın.

![Log Analytics çalışma alanı günlükleri dikey penceresi ' kullanılamayan bilgisayarlar ' örneği](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Tüm düğümler varsa, bu sorgu şimdilik 0 sonuç döndürmelidir. Bu sorgu için uyarınızı yapılandırmaya başlamak üzere **Yeni uyarı kuralı** ' na tıklayın.

![Log Analytics çalışma alanı yeni uyarı kuralı](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Bir uyarının üç bileşeni vardır: kuralın oluşturulacağı *kaynak* (bu durumda Log Analytics çalışma alanı), uyarının tetiklenmesi *koşulu* ve uyarı olduğunda ne olacağını belirleyen *eylem grupları* diğini.

Sinyal mantığını yapılandırmayı tamamlaması için aşağıda gösterildiği gibi **koşul başlığına**tıklayın.

![Portal uyarısı kural oluşturma koşulu](media/hdinsight-cluster-availability/portal-condition-title.png)

Bu işlem, **sinyal mantığını Yapılandır** dikey penceresini açar.

**Uyarı mantığı** bölümünü aşağıdaki şekilde ayarlayın:

*Temel alan: sonuç sayısı, koşul: büyüktür, eşik: 0.*

Bu sorgu yalnızca sonuç olarak kullanılamayan düğümleri döndürdüğünden, sonuç sayısı 0 ' dan büyük olursa uyarının tetiklenmesi gerekir.

**Değerlendirilen temelinde** bölümünde, kullanılamayan düğümleri ne sıklıkta denetlemek istediğinize göre **dönemi** ve **sıklığı** ayarlayın.

Bu uyarının amacına göre, **period = Frequency** olduğundan emin olmak istediğinizi unutmayın. Period, sıklık ve diğer uyarı parametreleri hakkında daha fazla bilgi [burada](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)bulunabilir.

Sinyal mantığını yapılandırmayı tamamladığınızda **bitti** ' ye tıklayın.

![Uyarı kuralı sinyal mantığını yapılandırma](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Zaten mevcut bir eylem grubunuz yoksa, **eylem grupları** bölümünde **Yeni oluştur** ' a tıklayın.

![Uyarı kuralı yeni eylem grubu oluştur](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Bu işlem, **eylem grubu Ekle** dikey penceresini açar. Bir **eylem grubu adı**, **kısa ad**, **abonelik**ve **kaynak grubu seçin.** **Eylemler** bölümünde, **eylem adı** ' nı seçin ve **eylem türü** olarak **e-posta/SMS/Push/ses'** i seçin.

> [!NOTE]
> Bir uyarının bir Azure Işlevi, LogicApp, Web kancası, ıTSM ve Otomasyon Runbook 'u gibi bir e-posta/SMS/Push/sesden farklı olarak tetikleyebileceği birkaç başka eylem vardır. [Daha fazla bilgi edinin.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Bu, **e-posta/SMS/Push/Voice** dikey penceresini açar. Alıcı için bir **ad** seçin, **e-posta** kutusunu **işaretleyin** ve uyarının gönderilmesini istediğiniz e-posta adresini yazın. **E-posta/SMS/Push/Voice** dikey penceresinde **Tamam** ' a tıklayın, sonra eylem grubunuzu yapılandırmayı sona erdirmeniz için **eylem grubu Ekle** dikey penceresinde.

![Uyarı kuralı ekleme eylem grubu oluştur](media/hdinsight-cluster-availability/portal-add-action-group.png)

Bu dikey pencereler kapatıldıktan sonra eylem **grupları** bölümünün altında listelenmiş eylem grubunuzu görmeniz gerekir. Son olarak, uyarı **ayrıntıları** bölümünü bir **Uyarı kuralı adı** ve **açıklaması** yazıp bir **önem derecesi**seçerek doldurun.
Bitiş için **Uyarı kuralı oluştur** ' a tıklayın.

![Portal Uyarı kuralı oluşturma bitiş](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> **Önem derecesi** belirtme özelliği, birden çok uyarı oluştururken kullanılabilecek güçlü bir araçtır. Örneğin, tek bir baş düğüm aşağı gittiğinde bir uyarı oluşturmak için bir uyarı (sev 1) ve her iki baş düğümün da önemli olmayan olayda kritik (sev 0) oluşturan başka bir uyarı oluşturabilirsiniz.

Bu uyarının koşulu karşılandığında, uyarı harekete geçmeyecektir ve uyarı ayrıntılarına şu şekilde bir e-posta alırsınız:

![Azure Izleyici uyarı e-postası örneği](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

Ayrıca, **Log Analytics çalışma alanınızdaki** **Uyarılar** dikey penceresine giderek,, önem derecesine göre gruplandırılan tüm uyarıları da görüntüleyebilirsiniz.

![Log Analytics çalışma alanı uyarıları](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Önem derecesine tıkladığınızda (yukarıda vurgulanan şekilde **sev 1),** bu önem derecesindeki tüm uyarılar için aşağıdaki gibi tetiklenen kayıtlar gösterilir:

![Log Analytics çalışma alanı önem derecesi 1 uyarıları](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Sonraki adımlar
- [HDInsight 'ta Apache Hadoop kümelerinin kullanılabilirliği ve güvenilirliği](hdinsight-high-availability-linux.md)
