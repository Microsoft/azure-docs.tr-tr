---
title: Azure HDInsight 'ı ambarı Web Kullanıcı arabirimini kullanarak izleme ve yönetme
description: HDInsight kümelerini izlemek ve yönetmek için Apache ambarı Kullanıcı arabirimini nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: 1b4570b09994f292de2b175396e4b6d1882658d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866497"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>HDInsight kümelerini Apache Ambari Web arabiriminden yönetme

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache ambarı, bir Apache Hadoop kümesinin yönetimini ve izlenmesini basitleştirir. Bu basitleştirme, Web Kullanıcı arabirimi ve REST API kullanımı kolay bir şekilde yapılır. Ambarı HDInsight kümelerine dahil edilmiştir ve kümeyi izlemek ve yapılandırma değişiklikleri yapmak için kullanılır.

Bu belgede, bir HDInsight kümesiyle ambarı Web Kullanıcı arabirimini kullanmayı öğreneceksiniz.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Apache ambarı nedir?

[Apache ambarı](https://ambari.apache.org) , kullanımı kolay bir Web Kullanıcı arabirimi sağlayarak Hadoop yönetimini basitleştirir. Hadoop kümelerini yönetmek ve izlemek için ambarı kullanabilirsiniz. Geliştiriciler, bu özellikleri, [AMBARı REST API 'lerini](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)kullanarak uygulamalarıyla tümleştirebilir.

## <a name="connectivity"></a>Bağlantı

Ambarı Web Kullanıcı arabirimi, adresindeki HDInsight kümenizde kullanılabilir; `https://CLUSTERNAME.azurehdinsight.net` burada `CLUSTERNAME` kümenizin adıdır.

> [!IMPORTANT]  
> HDInsight üzerinde ambarı 'na bağlanmak için HTTPS gerekir. Kimlik doğrulaması istendiğinde, küme oluşturulduğunda verdiğiniz yönetici hesabı adını ve parolasını kullanın. Kimlik bilgileriniz istenmezse, istemci ve Azure HDInsight kümeleri arasında bağlantı sorunu olmadığını doğrulamak için ağ ayarlarınızı denetleyin.

## <a name="ssh-tunnel-proxy"></a>SSH tüneli (proxy)

Kümenizin ambarı doğrudan Internet üzerinden erişilebilir olsa da, ambarı Web kullanıcı arabiriminden (örneğin, JobTracker gibi) bazı bağlantılar Internet 'te gösterilmez. Bu hizmetlere erişmek için bir SSH tüneli oluşturmanız gerekir. Daha fazla bilgi için bkz. [HDInsight Ile SSH tüneli kullanma](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambarı Web Kullanıcı arabirimi

> [!WARNING]  
> Tüm ambarı Web Kullanıcı arabiriminin özellikleri HDInsight üzerinde desteklenmez. Daha fazla bilgi için bu belgenin [Desteklenmeyen işlemler](#unsupported-operations) bölümüne bakın.

Ambarı Web Kullanıcı arabirimine bağlanırken, sayfada kimlik doğrulaması yapmanız istenir. Küme oluşturma sırasında kullandığınız küme Yönetici kullanıcısı (varsayılan yönetici) ve parolayı kullanın.

Sayfa açıldığında, üstteki çubuğun üzerine göz önünde yer. Bu çubuk aşağıdaki bilgileri ve denetimleri içerir:

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png" alt-text="Apache ambarı panosuna genel bakış":::

|Öğe |Açıklama |
|---|---|
|Ambarı logosu|Kümeyi izlemek için kullanılabilecek panoyu açar.|
|Küme adı # Ops|Devam eden ambarı işlemlerinin sayısını görüntüler. Küme adının seçilmesi veya **# Ops** , arka plan işlemlerinin bir listesini görüntüler.|
|uyarı sayısı|Küme için uyarıları veya kritik uyarıları görüntüler.|
|Pano|Panoyu görüntüler.|
|Hizmetler|Kümedeki hizmetler için bilgi ve yapılandırma ayarları.|
|Ana bilgisayarlar|Kümedeki düğümlerin bilgileri ve yapılandırma ayarları.|
|Uyarılar|Bilgi, uyarı ve kritik uyarıların günlüğü.|
|Yönetici|Kümede yüklü olan yazılım yığını/Hizmetleri, hizmet hesabı bilgileri ve Kerberos güvenliği.|
|Yönetici düğmesi|Ambarı yönetimi, Kullanıcı ayarları ve oturumu kapatma.|

## <a name="monitoring"></a>İzleme

### <a name="alerts"></a>Uyarılar

Aşağıdaki liste, ambarı tarafından kullanılan ortak uyarı durumlarını içerir:

* **Tamam**
* **Uyarı**
* **BAŞLATMA**
* **BILINMEYEN**

**Tamam** dışındaki uyarılar, uyarı sayısını göstermek için sayfanın en üstündeki **# Uyarı** girişine neden olur. Bu girdinin belirlenmesi, uyarıları ve bunların durumlarını görüntüler.

Uyarılar, **Uyarılar** sayfasından görüntülenebilen çeşitli varsayılan gruplar halinde düzenlenir.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png" alt-text="Apache ambarı uyarıları Sayfa Özeti":::

Grupları **Eylemler** menüsünü kullanarak yönetebilir ve **Uyarı gruplarını yönet**' i seçebilirsiniz.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png" alt-text="Apache ambarı uyarı gruplarını yönetme":::

Uyarı yöntemlerini yönetebilir ve __Bildirimleri Yönet__' i seçerek **Eylemler** menüsünde uyarı bildirimleri oluşturursunuz. Geçerli tüm bildirimler görüntülenir. Buradan bildirim oluşturun. Bildirimler, belirli bir uyarı/önem derecesi oluştuğunda **e-posta** veya **SNMP** aracılığıyla gönderilebilir. Örneğin, **Yarn varsayılan** grubundaki uyarılardan herhangi biri **kritik** olarak ayarlandığında bir e-posta iletisi gönderebilirsiniz.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png" alt-text="Apache ambarı oluşturma uyarı bildirimi":::

Son olarak, __Eylemler__ menüsünde __uyarı ayarlarını yönet__ ' i seçmek, bir uyarının bir bildirim gönderilmeden önce kaç kez oluşması gerektiğini ayarlamanıza olanak sağlar. Bu ayar, geçici hatalara yönelik bildirimleri engellemek için kullanılabilir.

Ücretsiz [SendGrid hesabı](../sendgrid-dotnet-how-to-send-email.md)kullanan bir uyarı bildirimi hakkında öğretici için bkz. [Azure HDInsight 'ta Apache ambarı e-posta bildirimlerini yapılandırma](./apache-ambari-email.md).

### <a name="cluster"></a>Küme

Panonun **ölçümler** sekmesi, kümenizin durumunu bir bakışta izlemeyi kolaylaştıran bir dizi pencere öğesi içerir. **CPU kullanımı** gibi birkaç pencere öğesi tıklandığında ek bilgi sağlar.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png" alt-text="Ölçümlerle Apache ambarı panosu":::

**Heatmaps** sekmesi, metrikleri renkli hecekmaps olarak, yeşil 'ten kırmızıya ve kırmızı bir şekilde görüntüler.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png" alt-text="Isı haritalarını ile Apache ambarı panosu":::

Küme içindeki düğümler hakkında daha fazla bilgi için **konaklar**' ı seçin. Ardından ilgilendiğiniz belirli bir düğümü seçin.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png" alt-text="Apache ambarı ana bilgisayar Özeti ayrıntıları":::

### <a name="services"></a>Hizmetler

Panodaki **Hizmetler** kenar çubuğu, kümede çalışan hizmetlerin durumuyla ilgili hızlı öngörüler sağlar. Yapılması gereken durum veya eylemleri göstermek için çeşitli simgeler kullanılır. Örneğin, bir hizmetin geri dönüştürülmesi gerekiyorsa sarı bir geri dönüşüm simgesi görüntülenir.


:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png" alt-text="Apache ambarı Hizmetleri yan çubuğu":::

> [!NOTE]  
> Görünen hizmetler, HDInsight küme türleri ve sürümleri arasında farklılık gösterir. Burada görüntülenecek hizmetler kümeniz için görüntülenecek hizmetlerden farklı olabilir.

Bir hizmetin seçilmesi, hizmet hakkında daha ayrıntılı bilgiler görüntüler.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png" alt-text="Apache ambarı hizmeti Özet bilgileri":::

#### <a name="quick-links"></a>Hızlı bağlantılar

Bazı hizmetler sayfanın üst kısmında bir **hızlı bağlantılar** bağlantısı görüntüler. Bu bağlantı, aşağıdaki gibi hizmete özgü Web 'e erişmek için kullanılabilir:

* **Iş geçmişi** -MapReduce iş geçmişi.
* **Kaynak Yöneticisi** -yarn kaynak YÖNETICISI Kullanıcı arabirimi.
* **Süs Code** -Hadoop Dağıtılmış dosya sistemi (suo) süs Yot Kullanıcı arabirimi.
* **Oozie Web Kullanıcı arabirimi** -OOZIE Kullanıcı arabirimi.

Bu bağlantılardan herhangi birini seçmek, tarayıcınızda seçili sayfayı görüntüleyen yeni bir sekme açar.

> [!NOTE]  
> Bir hizmetin **hızlı bağlantılar** girişini seçmek bir "sunucu bulunamadı" hatası döndürebilir. Bu hatayla karşılaşırsanız, bu hizmet için **hızlı bağlantılar** girişini KULLANıRKEN bir SSH tüneli kullanmanız gerekir. Bilgi için bkz. [HDInsight Ile SSH tüneli kullanma](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Yönetim

### <a name="ambari-users-groups-and-permissions"></a>Ambarı kullanıcıları, grupları ve izinleri

Kullanıcılar, gruplar ve izinlerle çalışma desteklenir. Yerel Yönetim için bkz. [kullanıcıları Apache ambarı görünümleri Için Yetkilendir](./hdinsight-authorize-users-to-ambari.md). Etki alanına katılmış kümeler için bkz. [etki alanına katılmış HDInsight kümelerini yönetme](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Linux tabanlı HDInsight kümenizdeki ambarı izleme (hdinsightwatchdog) parolasını silmeyin veya değiştirmeyin. Parola değiştirme, betik eylemlerini kullanma veya kümeniz ile ölçeklendirme işlemleri gerçekleştirme yeteneğini keser.

### <a name="hosts"></a>Ana bilgisayarlar

**Konaklar** sayfasında kümedeki tüm konaklar listelenir. Konakları yönetmek için aşağıdaki adımları izleyin.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png" alt-text="Apache ambarı ana bilgisayarları sayfasına genel bakış":::

> [!NOTE]  
> Bir konağın eklenmesi, kullanımdan kaldırılması ve yeniden oluşturulması HDInsight kümeleriyle kullanılmamalıdır.

1. Yönetmek istediğiniz Konağı seçin.

2. **Eylemler** menüsünü kullanarak yapmak istediğiniz eylemi seçin:

    |Öğe |Açıklama |
    |---|---|
    |Tüm bileşenleri Başlat|Konaktaki tüm bileşenleri başlatın.|
    |Tüm bileşenleri durdur|Konaktaki tüm bileşenleri durdurun.|
    |Tüm bileşenleri yeniden Başlat|Konaktaki tüm bileşenleri durdurup başlatın.|
    |Bakım modunu aç|Ana bilgisayar için uyarıları göstermez. Uyarı üreten eylemler yapıyorsanız bu mod etkinleştirilmelidir. Örneğin, bir hizmeti durdurup başlatma.|
    |Bakım modunu kapat|Ana bilgisayarı normal uyarı ' e döndürür.|
    |Durdur|Konakta Katanode veya Nodeyöneticileri sonlandırır.|
    |Başlangıç|Konakta Dadtanode veya Nodeyöneticileri başlatır.|
    |Yeniden başlat|Konakta Dadtanode veya Nodeyöneticileri durdurup başlatır.|
    |Açığa|Kümeden bir konağı kaldırır. **Bu eylemi HDInsight kümelerinde kullanmayın.**|
    |Yeniden komisyon|Önceden yetkisi alınmış bir konağı kümeye ekler. **Bu eylemi HDInsight kümelerinde kullanmayın.**|

### <a name="services"></a><a id="service"></a>Hizmetler

**Pano** veya **Hizmetler** sayfasında, tüm hizmetleri durdurmak ve başlatmak Için hizmetler listesinin altındaki **Eylemler** düğmesini kullanın.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png" alt-text="Apache ambarı hizmet eylemleri listesi." border="true":::

> [!WARNING]  
> Küme sağlama sırasında bir betik eylemi kullanılarak yeni hizmetler eklenmelidir. Betik eylemlerini kullanma hakkında daha fazla bilgi için bkz. [betik eylemlerini kullanarak HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md).

**Eylemler** düğmesi tüm hizmetleri yeniden başlatabiliyor olsa da, genellikle belirli bir hizmeti başlatmak, durdurmak veya yeniden başlatmak istersiniz. Tek bir hizmette eylem yapmak için aşağıdaki adımları kullanın:

1. **Pano** veya **Hizmetler** sayfasında bir hizmet seçin.

2. **Özet** sekmesinin en üstünden **hizmet eylemleri** düğmesini kullanın ve gerçekleştirilecek eylemi seçin. Bu eylem tüm düğümlerde hizmeti yeniden başlatır.

    :::image type="content" source="./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png" alt-text="Apache ambarı bireysel hizmet eylemleri":::

   > [!NOTE]  
   > Küme çalışırken bazı hizmetleri yeniden başlatmak uyarı oluşturabilir. Uyarıları önlemek için, yeniden başlatmayı gerçekleştirmeden önce hizmetin **bakım modunu** etkinleştirmek üzere **hizmet eylemleri** düğmesini kullanabilirsiniz.

3. Bir eylem seçildikten sonra, sayfanın en üstündeki **# op** girişi, bir arka plan işleminin gerçekleştiğini göstermek için artar. Görüntülenecek şekilde yapılandırıldıysa, arka plan işlemleri listesi görüntülenir.

   > [!NOTE]  
   > Hizmet için **bakım modunu** etkinleştirdiyseniz, işlem tamamlandıktan sonra **hizmet eylemleri** düğmesini kullanarak bunu devre dışı bırakmayı unutmayın.

Bir hizmeti yapılandırmak için aşağıdaki adımları kullanın:

1. **Pano** veya **Hizmetler** sayfasında bir hizmet seçin.

2. Yapılandırma sekmesini **seçin** . Geçerli yapılandırma görüntülenir. Önceki yapılandırmaların bir listesi de görüntülenir.

    :::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png" alt-text="Apache ambarı hizmeti yapılandırması":::

3. Yapılandırmayı değiştirmek için görüntülenecek alanları kullanın ve ardından **Kaydet**' i seçin. Veya önceki ayarlara geri dönmek için önceki bir yapılandırma seçin ve ardından **geçerli yap** ' ı seçin.

## <a name="ambari-views"></a>Ambarı görünümleri

Ambarı görünümleri, geliştiricilerin Apache ambarı görünümleri çerçevesini kullanarak UI öğelerini ambarı Web Kullanıcı arabirimine eklemesine olanak tanır. HDInsight, Hadoop kümesi türleriyle aşağıdaki görünümleri sağlar:

* Hive görünümü: Hive görünümü, Hive sorgularını doğrudan Web tarayıcınızdan çalıştırmanızı sağlar. Sorguları kaydedebilir, sonuçları görüntüleyebilir, sonuçları küme depolamasına kaydedebilir veya sonuçları yerel sisteminize indirebilirsiniz. Hive Görünümlerini kullanma hakkında daha fazla bilgi için bkz. [HDInsight ile Apache Hive Görünümlerini kullanma](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Tez görünümü: tez görünümü işleri daha iyi anlamanıza ve iyileştirmenize olanak tanır. Tez işlerinin nasıl yürütüldüğü ve hangi kaynakların kullanıldığı hakkında bilgi görüntüleyebilirsiniz.

## <a name="unsupported-operations"></a>Desteklenmeyen işlemler

HDInsight 'ta aşağıdaki ambarı işlemleri desteklenmez:

* __Ölçüm Toplayıcı hizmeti taşınıyor__. Ölçüm toplayıcı hizmetindeki bilgileri görüntülerken, hizmet eylemleri menüsünde bulunan eylemlerden biri __ölçüm toplayıcıyı taşıma__' dır. Bu eylem HDInsight 'ta desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

* HDInsight ile [Apache ambarı REST API](hdinsight-hadoop-manage-ambari-rest-api.md) .
* [HDInsight kümesi yapılandırmalarını iyileştirmek için Apache Ambari'yi kullanma](./hdinsight-changing-configs-via-ambari.md)
* [Azure HDInsight kümelerini ölçeklendirme](./hdinsight-scaling-best-practices.md)
