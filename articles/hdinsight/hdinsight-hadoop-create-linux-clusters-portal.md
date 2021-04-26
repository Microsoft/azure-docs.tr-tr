---
title: Web tarayıcısı, Azure HDInsight kullanarak Apache Hadoop kümeleri oluşturma
description: HDInsight üzerinde Apache Hadoop, Apache HBase, Apache Storm veya Apache Spark kümeleri oluşturmayı öğrenin. Web tarayıcısını ve Azure portal kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/06/2020
ms.openlocfilehash: 98d18044693bc6f4d23e04e9403e71686df39451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872209"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure portal kullanarak HDInsight 'ta Linux tabanlı kümeler oluşturun

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure portal, Microsoft Azure bulutta barındırılan hizmetler ve kaynaklar için Web tabanlı bir yönetim aracıdır. Bu makalede, portalı kullanarak Linux tabanlı Azure HDInsight kümeleri oluşturmayı öğreneceksiniz. [HDInsight kümeleri oluşturma](./hdinsight-hadoop-provision-linux-clusters.md)' dan ek ayrıntılar bulunabilir.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure portal, küme özelliklerinin çoğunu gösterir. Azure Resource Manager şablonları kullanarak, birçok ayrıntıyı gizleyebilirsiniz. Daha fazla bilgi için bkz. [HDInsight 'ta Kaynak Yöneticisi şablonları kullanarak Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-clusters"></a>Küme oluşturma

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Üstteki menüden **+ kaynak oluştur**' u seçin.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png" alt-text="Azure portal yeni bir küme oluşturun":::

1. **Analiz**  >  **Azure HDInsight** ' ı seçerek **HDInsight kümesi oluşturma** sayfasına gidin.

## <a name="basics"></a>Temel Bilgiler

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png" alt-text="HDInsight küme oluşturma temelleri":::

**Temel bilgiler** sekmesinde, aşağıdaki bilgileri sağlayın:

|Özellik |Açıklama |
|---|---|
|Abonelik|Aşağı açılan listeden, küme için kullanılan Azure aboneliğini seçin.|
|Kaynak grubu|Aşağı açılan listeden, mevcut kaynak grubunuzu seçin veya **Yeni oluştur**' u seçin.|
|Küme adı|Genel olarak benzersiz bir ad girin.|
|Region|Aşağı açılan listeden, kümenin oluşturulduğu bir bölge seçin.|
|Küme türü|Bir listeyi açmak için **küme türünü seç** ' e tıklayın. Listeden istenen küme türünü seçin. HDInsight kümeleri farklı türlerde gelir. Bunlar, kümenin ayarlanmış olduğu iş yüküne veya teknolojiye karşılık gelir. Birden çok türü birleştiren bir küme oluşturmak için desteklenen bir yöntem yoktur.|
|Sürüm|Aşağı açılan listeden bir **Sürüm** seçin. Ne seçeceğimizi bilmiyorsanız varsayılan sürümü kullanın. Daha fazla bilgi için bkz. [HDInsight küme sürümleri](hdinsight-component-versioning.md).|
|Küme oturum açma kullanıcı adı|Kullanıcı adını belirtin, varsayılan olarak **admin**' dir.|
|Küme oturum açma parolası|Parolayı girin.|
|Küme oturum açma parolasını onaylayın|Parolayı yeniden girin|
|Secure Shell (SSH) kullanıcı adı|Kullanıcı adını belirtin, varsayılan olarak **sshuser**|
|SSH için küme oturum açma parolasını kullanma|Daha önce belirttiğiniz yönetici parolasıyla aynı SSH parolasını istiyorsanız **SSH için küme oturum açma parolasını kullan** onay kutusunu işaretleyin. Aksi durumda, SSH kullanıcısının kimliğini doğrulamak için bir **parola** veya **ortak anahtar** sağlayın. Ortak anahtar, önerdiğimiz yaklaşımdır. Kimlik bilgileri yapılandırmasını kaydetmek için en altta **Seç ' i** seçin.  Daha fazla bilgi için bkz. [SSH kullanarak HDInsight 'a (Apache Hadoop) bağlanma](hdinsight-hadoop-linux-use-ssh-unix.md).|

Ileri ' yi seçin: bir sonraki sekmeye ilerlemek için **depolama >>** .

## <a name="storage"></a>Depolama

> [!WARNING] 
> 15 Haziran 2020 ' den itibaren müşteriler HDInsight kullanarak yeni hizmet sorumlusu oluşturamayacak. Bkz. Azure Active Directory kullanarak [hizmet sorumlusu ve sertifikaları oluşturma](../active-directory/develop/howto-create-service-principal-portal.md) .

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png" alt-text="HDInsight küme depolaması oluştur":::

### <a name="primary-storage"></a>Birincil depolama alanı

**Birincil depolama türü** için aşağı açılan listeden varsayılan depolama türünü seçin. Tamamlanacak sonraki alanlar, seçiminize göre farklılık gösterecektir. **Azure depolama** için:

1. **Seçim yöntemi** Için **listeden seç**' i veya **erişim anahtarı**' nı seçin.
    * **Listeden seç**' in ardından, açılan listeden **birincil depolama hesabınızı** seçin veya **Yeni oluştur**' u seçin.
    * **Kullanım erişim anahtarı** için **depolama hesabınızın adını** girin. Daha sonra **erişim anahtarını** sağlayın.

1. **Kapsayıcı** için varsayılan değeri kabul edin veya yeni bir tane girin.

### <a name="additional-azure-storage"></a>Ek Azure depolama

İsteğe bağlı: ek küme depolaması için **Azure depolama Ekle** ' yi seçin. HDInsight kümesinden farklı bir bölgede ek depolama hesabı kullanılması desteklenmez.

### <a name="metastore-settings"></a>Meta veri deposu ayarları

İsteğe bağlı: küme dışında Apache Hive, Apache Oozie, ve veya Apache ambarı meta verilerini kaydetmek için mevcut bir SQL veritabanı belirtin. Meta veri deposu için kullanılan Azure SQL veritabanı, Azure HDInsight da dahil olmak üzere diğer Azure hizmetleriyle bağlantı kurulmasına izin vermelidir. Bir meta veri deposu oluşturduğunuzda bir veritabanını tireler veya kısa çizgilerden adlandırma. Bu karakterler, küme oluşturma işleminin başarısız olmasına neden olabilir.

> [!IMPORTANT]
> Metastores 'yi destekleyen küme şekilleri için varsayılan meta veri deposu, **temel katman 5 DTU sınırı (yükseltilebilir)** Ile BIR Azure SQL veritabanı sağlar! Temel test amaçları için uygundur. Büyük veya üretim iş yükleri için bir dış meta veri yüklemeye geçiş yapmanızı öneririz.

Sonraki sekmeye ilerlemek için **İleri ' yi seçin: güvenlik + ağ >>** .

## <a name="security--networking"></a>Güvenlik + ağ

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png" alt-text="HDInsight küme güvenliği ağı oluşturma":::

**Güvenlik + ağ** sekmesinden aşağıdaki bilgileri sağlayın:

|Özellik |Açıklama |
|---|---|
|Kurumsal güvenlik paketi|İsteğe bağlı: **Kurumsal güvenlik paketi** kullanmak için onay kutusunu seçin. Daha fazla bilgi için [Azure Active Directory Domain Services kullanarak kurumsal güvenlik paketi HDInsight kümesi yapılandırma](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)konusuna bakın.|
|TLS|İsteğe bağlı: açılan listeden bir TLS sürümü seçin. Daha fazla bilgi için bkz. [Aktarım Katmanı Güvenliği](./transport-layer-security.md).|
|Sanal ağ|İsteğe bağlı: açılan listeden var olan bir sanal ağı ve alt ağı seçin. Bilgi için bkz. [Azure HDInsight kümeleri için sanal ağ dağıtımını planlayın](hdinsight-plan-virtual-network-deployment.md). Bu makale, sanal ağ için belirli yapılandırma gereksinimlerini içerir.|
|Disk şifreleme ayarları|İsteğe bağlı: şifrelemeyi kullanmak için onay kutusunu seçin. Daha fazla bilgi için bkz. [müşteri tarafından yönetilen anahtar disk şifrelemesi](./disk-encryption.md).|
|Kafka REST ara sunucusu|Bu ayar yalnızca Kafka küme türü için kullanılabilir. Daha fazla bilgi için bkz. [rest proxy kullanma](./kafka/rest-proxy.md).|
|Kimlik|İsteğe bağlı: açılan listeden var olan bir kullanıcı tarafından atanan hizmet kimliğini seçin. Daha fazla bilgi için bkz. [Azure HDInsight 'Ta Yönetilen kimlikler](./hdinsight-managed-identities.md).|

Sonraki sekmeye ilerlemek için **İleri ' yi seçin: yapılandırma + fiyatlandırma >>** .

## <a name="configuration--pricing"></a>Yapılandırma + fiyatlandırma

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png" alt-text="HDInsight küme yapılandırması oluştur":::

**Yapılandırma + fiyatlandırma** sekmesinden aşağıdaki bilgileri sağlayın:

|Özellik |Açıklama |
|---|---|
|+ Uygulama Ekle|İsteğe bağlı: istediğiniz uygulamaları seçin. Microsoft, bağımsız yazılım satıcıları (ISV) veya bu uygulamaları geliştirebilirsiniz. Daha fazla bilgi için bkz. [küme oluşturma sırasında uygulamaları yüklemeyi](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Düğüm boyutu|İsteğe bağlı: farklı boyutlardaki bir düğüm seçin.|
|Düğüm sayısı|İsteğe bağlı: belirtilen düğüm türü için düğüm sayısını girin. 32 ' den fazla çalışan düğümünü planlıyorsanız en az sekiz çekirdek ve 14 GB RAM içeren bir baş düğüm boyutu seçin. Düğümleri küme oluşturma sırasında veya oluşturulduktan sonra kümeyi ölçeklendirerek planlayın.|
|Otomatik ölçeklendirmeyi etkinleştir|İsteğe bağlı: özelliği etkinleştirmek için onay kutusunu seçin. Daha fazla bilgi için bkz. [Azure HDInsight kümelerini otomatik olarak ölçeklendirme](./hdinsight-autoscale-clusters.md).|
|+ Betik eylemi Ekle|İsteğe bağlı: Bu seçenek, küme oluşturulduğu için bir kümeyi özelleştirmek üzere özel bir komut dosyası kullanmak istiyorsanız kullanılır. Betik eylemleri hakkında daha fazla bilgi için bkz. [betik eylemlerini kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md).|

Küme yapılandırmasını doğrulamak ve son sekmeye ilerlemek için **gözden geçir + oluştur >>** seçin.

## <a name="review--create"></a>Gözden geçirme ve oluşturma

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png" alt-text="HDInsight küme Özeti oluştur":::

Ayarları gözden geçirin. **Oluştur**’u seçerek kümeyi oluşturun.

Kümenin oluşturulması genellikle yaklaşık 20 dakika sürer. Sağlama işlemini denetlemek için **bildirimleri** izleyin.

## <a name="post-creation"></a>Oluşturma sonrası

Oluşturma işlemi bittikten sonra **dağıtım başarılı** bildiriminde **Kaynağa Git** ' i seçin. Küme penceresi aşağıdaki bilgileri sağlar.

:::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png" alt-text="HDI Azure portal kümesine genel bakış":::

Penceredeki simgelerden bazıları şu şekilde açıklanmıştır:

|Özellik | Açıklama |
|---|---|
|Genel Bakış|Kümeyle ilgili tüm gerekli bilgileri sağlar. Örnek olarak ad, ait olduğu kaynak grubu, konum, işletim sistemi ve küme panosunun URL 'SI verilebilir.|
|Küme panoları|Sizi kümeyle ilişkili olan ambarı portalına yönlendirir.|
|SSH + küme oturumu açma|SSH kullanarak kümeye erişmek için gereken bilgileri sağlar.|
|Sil|HDInsight kümesini siler.|

## <a name="delete-the-cluster"></a>Küme silme

Bkz. [tarayıcınızı, PowerShell 'i veya Azure CLI 'yı kullanarak HDInsight kümesini silme](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Sorun giderme

HDInsight kümeleri oluştururken sorun yaşarsanız bkz. [erişim denetimi gereksinimleri](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Sonraki adımlar

Bir HDInsight kümesini başarıyla oluşturdunuz. Şimdi kümenizle nasıl çalışacağınızı öğrenin.

* [HDInsight ile Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
* [HDInsight 'ta Apache HBase ile çalışmaya başlama](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Betik eylemlerini kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md)