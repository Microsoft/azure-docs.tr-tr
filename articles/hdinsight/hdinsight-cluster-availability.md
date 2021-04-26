---
title: Azure HDInsight 'ta Apache ambarı ile küme kullanılabilirliğini izleme
description: Küme durumunu ve kullanılabilirliğini izlemek için Apache ambarı 'nı nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: dcbe44defeb89ad2f67833b263e5f4983070a46c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863539"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache ambarı ile küme kullanılabilirliğini izleme

HDInsight kümeleri, bir bakışta sağlık bilgileri ve önceden tanımlanmış uyarılar sağlayan Apache ambarı 'nı içerir.

Bu makalede, kümenizi izlemek ve bir bir veya daha fazla düğümden beş saat içinde bir sinyal alınmadığında başlatılan bir Azure Izleyici uyarısı oluşturmak için ambarı 'nın nasıl kullanılacağı gösterilmektedir.

## <a name="dashboard"></a>Pano

**Ambarı panosuna** aşağıda gösterildiği gibi, HDInsight genel bakış konusunun **küme panoları** bölümünde bulunan Azure Portal. Alternatif olarak, `https://CLUSTERNAME.azurehdinsight.net` clustername öğesinin Kümenizin adı olduğu bir tarayıcıda gezinilerek erişilebilir.

:::image type="content" source="media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png" alt-text="HDInsight kaynak portalı görünümü":::

Daha sonra bir küme oturum açma Kullanıcı adı ve parolası istenir. Kümeyi oluştururken seçtiğiniz kimlik bilgilerini girin.

Daha sonra, HDInsight kümenizin sistem durumuna hızlı bir genel bakış sunmak için bir dizi ölçümü gösteren pencere öğeleri içeren, daha sonra bir ambarı panosuna götürülürsünüz. Bu pencere öğeleri, Spark ve Hadoop kümeleri için YARN ResourceManager çalışma süresi gibi belirli küme türlerine özgü ölçümler ve canlı düğüm sayısı (çalışan düğümleri) ve JournalNodes (Zookeeper node), Üçlü iş çalışma süresi (örneğin, çok sayıda) gibi ölçümleri gösterir.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-dashboard.png" alt-text="Apache ambarı kullanım panosu görüntüleme":::

## <a name="hosts--view-individual-node-status"></a>Konaklar – tek tek düğüm durumunu görüntüle

Tek tek düğümlerin durum bilgilerini de görüntüleyebilirsiniz. Kümenizdeki tüm düğümlerin listesini görüntülemek için **konaklar** sekmesini seçin ve her düğüm hakkında temel bilgileri görüntüleyin. Her düğüm adının solunda yeşil onay işareti, tüm bileşenlerin düğüm üzerinde olduğunu gösterir. Bir bileşen düğüm üzerinde kapalıysa yeşil onay yerine kırmızı bir uyarı üçgeni görürsünüz.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts1.png" alt-text="HDInsight Apache ambarı Konakları görünümü":::

Daha sonra söz konusu düğüme ait daha ayrıntılı konak ölçümlerini görüntülemek için bir düğüm **adı** ' nı seçebilirsiniz. Bu görünüm her bir bileşenin durumunu/kullanılabilirliğini gösterir.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts-node.png" alt-text="Apache ambarı tek düğümlü görünüm barındırır":::

## <a name="ambari-alerts"></a>Ambarı uyarıları

Ayrıca, belirli olaylara yönelik bildirim sağlayabilen birkaç yapılandırılabilir uyarı da sunar. Uyarılar tetiklendiğinde, bu uyarılar, bu uyarı sayısını içeren kırmızı bir rozet içinde, ambarı 'nın sol üst köşesinde gösterilir. Bu rozet seçildiğinde geçerli uyarıların bir listesi gösterilir.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-alerts.png" alt-text="Apache ambarı geçerli uyarı sayısı":::

Uyarı tanımlarının ve durumlarının listesini görüntülemek için, aşağıda gösterildiği gibi **Uyarılar** sekmesini seçin.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alerts-definitions.png" alt-text="Ambarı uyarı tanımları görünümü":::

Ambarı aşağıdakiler dahil olmak üzere kullanılabilirliğiyle ilgili çok sayıda önceden tanımlı uyarı sunar:

| Uyarı Adı                        | Description   |
|---|---|
| Dadtanode sistem durumu Özeti           | Sağlıksız bir kades varsa, bu hizmet düzeyi uyarı tetiklenir|
| Süs Yot yüksek kullanılabilirlik durumu | Bu hizmet düzeyi uyarı, etkin bir süs Code veya standby süs ODE çalışmıyorsa tetiklenir.|
| Kullanılabilir JournalNodes yüzdesi    | Bu uyarı, kümedeki aşağı yönelik JournalNodes sayısı yapılandırılan kritik eşikten fazlaysa tetiklenir. Bu, JournalNode işlem denetimlerinin sonuçlarını toplar. |
| Kullanılabilir gün yüzdesi       | Bu uyarı, kümedeki aşağı doğru eşik sayısı yapılandırılan kritik eşikten büyükse tetiklenir. Bu, Davtanode işlem denetimlerinin sonuçlarını toplar.|


Bir uyarının veya değişiklik ölçütlerinin ayrıntılarını görüntülemek için uyarının **adını** seçin. Örnek olarak, **Davode sistem durumu özetini** alın. Bir ' uyarı ' veya ' kritik ' uyarı ve kriterlerin denetim aralığı tetiklenecek belirli ölçütlere ek olarak uyarının açıklamasını görebilirsiniz. Yapılandırmayı düzenlemek için yapılandırma kutusunun sağ üst köşesindeki **Düzenle** düğmesini seçin.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration.png" alt-text="Apache ambarı uyarı yapılandırması":::

Burada, açıklamayı ve uyarı ya da kritik uyarılar için denetim aralığını ve eşikleri düzenleyebilirsiniz.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png" alt-text="Ambarı uyarı yapılandırması düzenleme görünümü":::

Bu örnekte, önemli bir uyarı tetikleyebilmeniz ve 1 sağlıksız bir ıtanode yalnızca uyarı tetikleyebilmeniz gerekir. Düzenlemeden sonra **Kaydet** ' i seçin.

## <a name="email-notifications"></a>E-posta bildirimleri

Ayrıca, isteğe bağlı olarak, ambarı uyarıları için e-posta bildirimleri yapılandırabilirsiniz. Bunu yapmak için, **Uyarılar** sekmesinde, sol üst köşedeki **Eylemler** düğmesine ve ardından **Bildirimleri Yönet** ' e tıklayın.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-manage-notifications.png" alt-text="Ambarı yönetimi bildirimleri eylemi":::

Uyarı bildirimlerini yönetmek için bir iletişim kutusu açılır. İletişim kutusunun alt kısmındaki öğesini seçin **+** ve e-posta göndermek için e-posta sunucusu ayrıntılarını kullanarak, ambarı sağlamak üzere gerekli alanları doldurun.

> [!TIP]
> Ambarı e-posta bildirimlerinin kurulması, birçok HDInsight kümesini yönetirken tek bir yerde uyarı almanın iyi bir yoludur.

## <a name="next-steps"></a>Sonraki adımlar

- [HDInsight 'ta Apache Hadoop kümelerinin kullanılabilirliği ve güvenilirliği](./hdinsight-business-continuity.md)
- [Küme kullanılabilirliği - Azure İzleyici günlükleri](./cluster-availability-monitor-logs.md)
- [Azure Izleyici günlüklerini kullanma](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Apache Ambari e-posta bildirimleri](apache-ambari-email.md)
