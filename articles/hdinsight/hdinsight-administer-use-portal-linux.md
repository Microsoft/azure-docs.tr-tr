---
title: Azure portal kullanarak HDInsight 'ta Apache Hadoop kümelerini yönetme
description: Azure portal kullanarak Azure HDInsight kümeleri oluşturmayı ve yönetmeyi öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 4cf979c99c596abff075eb38f3358c6389a6b07e
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264278"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>HDInsight 'ta Apache Hadoop kümelerini Azure portal kullanarak yönetin

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure Portal](https://portal.azure.com)kullanarak, Azure hdınsight 'ta [Apache Hadoop](https://hadoop.apache.org/) kümelerini yönetebilirsiniz. Diğer araçları kullanarak HDInsight 'ta Hadoop kümelerini yönetme hakkında bilgi için yukarıdaki sekme seçiciyi kullanın.

## <a name="prerequisites"></a>Prerequisites

HDInsight 'ta mevcut bir Apache Hadoop kümesi.  Bkz. [HDInsight 'ta Azure Portal kullanarak Linux tabanlı kümeler oluşturma](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Başlarken

[@No__t-1](https://portal.azure.com)' de oturum açın.

## <a name="showClusters"></a>Kümeleri listeleme ve gösterme

**HDInsight kümeleri** sayfası, mevcut kümelerinizi listeler.  Portaldan:
1. Sol taraftaki menüden **tüm hizmetler** ' i seçin.
2. **Analiz**altında **HDInsight kümeleri** ' ni seçin.

## <a name="homePage"></a>Küme giriş sayfası 

[**HDInsight kümeleri**](#showClusters) sayfasından kümenizin adını seçin.  Bu işlem, aşağıdaki görüntüye benzer şekilde görünen **genel bakış** görünümünü açar:

![Azure portal HDInsight kümesi temel bileşenleri](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Üst menü:**  

| Öğe| Açıklama |
|---|---|
|Geçiş|Kümeyi başka bir kaynak grubuna veya başka bir aboneliğe kaydırır.|
|Sil|Kümeyi siler. |
|Yenile|Görünümü yeniler.|

**Sol menü:**  

  - **Sol üst menü**

    | Öğe| Açıklama |
    |---|---|
    |Genel bakış|Kümeniz için genel bilgiler sağlar.|
    |Etkinlik günlüğü|Etkinlik günlüklerini gösterir ve sorgular.|
    |Erişim denetimi (ıAM)|Rol atamalarını kullanın.  Bkz. [Azure abonelik kaynaklarınıza erişimi yönetmek için rol atamalarını kullanma](../role-based-access-control/role-assignments-portal.md).|
    |Etiketler|Bulut hizmetlerinizin özel bir taksonomini tanımlamak için anahtar/değer çiftleri ayarlamanıza olanak sağlar. Örneğin, **Proje**adlı bir anahtar oluşturabilir ve ardından belirli bir projeyle ilişkili tüm hizmetler için ortak bir değer kullanabilirsiniz.|
    |Sorunları tanılama ve çözme|Sorun giderme bilgilerini görüntüleyin.|
    |Hızlı başlangıç|HDInsight kullanmaya başlamanıza yardımcı olacak bilgiler görüntüler.|
    |Araçlar|HDInsight ile ilgili araçlar için yardım bilgileri.|

  - **Ayarlar menüsü**  

    | Öğe| Açıklama |
    |---|---|
    |Küme boyutu|Küme çalışan düğümlerinin sayısını denetleyin, artırın ve azaltın. Bkz. [Ölçek Kümeleri](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Kota sınırları|Aboneliğiniz için kullanılan ve kullanılabilir çekirdekleri görüntüleyin.|
    |SSH + küme oturumu açma|Secure Shell (SSH) bağlantısı kullanarak kümeye bağlanma yönergelerini gösterir. Daha fazla bilgi için bkz. [HDInsight Ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage 1.|Erişim Data Lake Storage 1. yapılandırma.  Bkz. [hızlı başlangıç: HDInsight 'ta kümeleri ayarlama](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).|
    |Depolama hesapları|Depolama hesaplarını ve anahtarları görüntüleyin. Depolama hesapları, küme oluşturma işlemi sırasında yapılandırılır.|
    |Uygulamalar|HDInsight uygulamaları ekleyin/kaldırın.  Bkz. [özel HDInsight uygulamaları yüklemeyi](hdinsight-apps-install-custom-applications.md).|
    |Betik eylemleri|Kümede Bash betiklerini çalıştırın. Bkz. [betik eylemi kullanarak Linux tabanlı HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md).|
    |Dış meta veri|[Apache Hive](https://hive.apache.org/) ve [Apache Oozie](https://oozie.apache.org/) meta tastores ' nı görüntüleyin. Meta depolar yalnızca küme oluşturma işlemi sırasında yapılandırılabilir.|
    |HDInsight iş ortağı|Geçerli HDInsight Iş ortağını ekleyin/kaldırın.|
    |Özellikler|[Küme özelliklerini](#properties)görüntüleyin.|
    |Kaynaktaki|Kümenin değiştirilmesini veya silinmesini engellemek için bir kilit ekleyin.|
    |Şablonu dışarı aktar|Küme için Azure Resource Manager şablonunu görüntüleyin ve dışarı aktarın. Şu anda yalnızca bağımlı Azure Storage hesabını dışarı aktarabilirsiniz. Bkz. [HDInsight 'ta Azure Resource Manager şablonları kullanarak Linux tabanlı Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **İzleme menüsü**

    | Öğe| Açıklama |
    |---|---|
    |Uyarılar|Uyarıları ve eylemleri yönetin.|
    |Ölçümler|Azure Izleyici günlüklerinde küme ölçümlerini izleyin.|
    |Tanılama ayarları|Tanılama ölçümlerinin depolanacağı ayarlar.|
    |Azure Izleyici|Azure Izleyici 'de kümenizi izleyin.|

  - **Destek + sorun giderme menüsü**

    | Öğe| Açıklama |
    |---|---|
    |Kaynak durumu|Bkz. [Azure Kaynak durumu genel bakış](../service-health/resource-health-overview.md).|
    |Yeni destek isteği|Microsoft desteği ile bir destek bileti oluşturmanıza olanak sağlar.|

## <a name="properties"></a>Küme özellikleri

[Küme giriş sayfasında](#homePage), **Ayarlar** ' ın altında **Özellikler**' i seçin.

|Öğe | Açıklama |
|---|---|
|Konak|Küme adı.|
|KÜME URL 'SI|Ambarı Web arabiriminin URL 'SI.|
|Özel uç nokta|Küme için özel uç nokta.|
|Güvenli Kabuk (SSH)|SSH aracılığıyla kümeye erişirken kullanılacak Kullanıcı adı ve ana bilgisayar adı.|
|DURUMLARıNA|Bunlardan biri: durduruldu, kabul edildi, Clusterstoragesağlandı, AzureVMConfiguration, HDInsightConfiguration, Işlemsel, çalışıyor, hata, silme, silinen, zaman aşımına uğradı, Deletekuyruklanmış, Deletetimemak, DeleteError, Patchkuyruktaki, Certrolloverkuyrukta, Resizekuyruğa alındı veya ClusterCustomization.|
|GELI|Azure konumu. Desteklenen Azure konumlarının listesi için bkz. [HDInsight fiyatlandırması](https://azure.microsoft.com/pricing/details/hdinsight/)üzerinde **bölge** açılan liste kutusu.|
|OLUŞTURULMA TARIHI|Kümenin dağıtıldığı tarih.|
|İşletim Sistemi|**Windows** ya da **Linux**.|
|TÜRÜYLE|Hadoop, HBase, fırtınası, Spark.|
|Version|Bkz. [HDInsight sürümleri](hdinsight-component-versioning.md).|
|ABONELIĞINIZ|Abonelik adı.|
|VARSAYıLAN VERI KAYNAĞı|Varsayılan küme dosya sistemi.|
|Çalışan düğümleri boyutları|Çalışan düğümlerinin seçili VM boyutu.|
|Baş düğüm boyutu|Baş düğümlerin seçili VM boyutu.|
|Sanal ağ|Dağıtım zamanında seçilmiş olan kümenin dağıtıldığı sanal ağın adı.|

## <a name="move-clusters"></a>Kümeleri taşı

HDInsight kümesini başka bir Azure Kaynak grubuna veya başka bir aboneliğe taşıyabilirsiniz.

[Küme giriş sayfasından](#homePage):

1. Üstteki menüden **Taşı** ' yı seçin.
2. **Başka bir kaynak grubuna Taşı** veya **başka bir aboneliğe taşı**' yı seçin.
3. Yeni sayfadaki yönergeleri izleyin.

## <a name="delete-clusters"></a>Kümeleri Sil

Bir kümeyi silmek varsayılan depolama hesabını ve bağlı depolama hesaplarını silmez. Aynı depolama hesaplarını ve aynı meta tasmres 'yi kullanarak kümeyi yeniden oluşturabilirsiniz. Kümeyi yeniden oluşturduğunuzda yeni bir varsayılan blob kapsayıcısı kullanmanızı öneririz.

[Küme giriş sayfasından](#homePage):

1. Üstteki menüden **Sil** ' i seçin.
2. Yeni sayfadaki yönergeleri izleyin.

Ayrıca bkz. [kümeleri duraklatma/kapatma](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Ek depolama hesapları ekleme

Bir küme oluşturulduktan sonra ek Azure depolama hesapları ve Azure Data Lake Storage hesapları ekleyebilirsiniz. Daha fazla bilgi için bkz. [HDInsight 'a ek depolama hesapları ekleme](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Ölçek Kümeleri

Küme ölçekleme özelliği, kümeyi yeniden oluşturmaya gerek kalmadan bir Azure HDInsight kümesi tarafından kullanılan çalışan düğümlerinin sayısını değiştirmenize olanak sağlar.

Tüm bilgiler için bkz. [HDInsight kümelerini ölçeklendirme](./hdinsight-scaling-best-practices.md) .

## <a name="pauseshut-down-clusters"></a>Kümeleri duraklatma/kapatma

Hadoop işlerinin çoğu yalnızca zaman zaman çalışan toplu işlerdir. Çoğu Hadoop kümesi için, kümenin işlenmek üzere kullanılmayan büyük süreler vardır. HDInsight ile Verileriniz Azure Storage 'da depolanır, bu sayede bir kümeyi kullanımda olmadığında güvenle silebilirsiniz.
Ayrıca, kullanımda olmasa bile bir HDInsight kümesi için de ücretlendirilirsiniz. Kümenin ücretleri depolama ücretinden çok daha fazla olduğundan, kullanımda olmadıkları zaman kümeleri silmek ekonomik bir anlam sağlar.

İşlemi programlayabilirsiniz. birçok yol vardır:

- Kullanıcı Azure Data Factory. İsteğe bağlı HDInsight bağlı hizmetleri oluşturmak için [Azure Data Factory kullanarak HDInsight 'ta isteğe bağlı Linux tabanlı Apache Hadoop kümeleri oluşturma](hdinsight-hadoop-create-linux-clusters-adf.md) konusuna bakın.
- Azure PowerShell kullanın.  Bkz. [Uçuş gecikmesi verilerini çözümleme](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Azure CLı 'yi kullanın. Bkz. [Azure CLI kullanarak Azure HDInsight kümelerini yönetme](hdinsight-administer-use-command-line.md).
- HDInsight .NET SDK 'Yı kullanın. Bkz. [Apache Hadoop Işleri gönderme](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Fiyatlandırma bilgileri için bkz. [HDInsight fiyatlandırması](https://azure.microsoft.com/pricing/details/hdinsight/). Portaldan bir kümeyi silmek için bkz. [kümeleri silme](#delete-clusters)

## <a name="upgrade-clusters"></a>Küme yükseltme

Bkz. [HDInsight kümesini daha yeni bir sürüme yükseltme](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Apache ambarı Web Kullanıcı arabirimini açın

Ambarı, tekrar eden API 'Ler tarafından desteklenen sezgisel, kullanımı kolay bir Hadoop yönetimi Web Kullanıcı arabirimi sağlar. Ambarı, sistem yöneticilerinin Hadoop kümelerini yönetmesine ve izlemesine olanak sağlar.

[Küme giriş sayfasından](#homePage):

1. **Küme panoları**' nı seçin.

    ![HDInsight Apache Hadoop küme menüsü](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Yeni sayfadan **ambarı giriş** ' i seçin.
1. Küme Kullanıcı adı ve parolasını girin.  Varsayılan küme Kullanıcı adı _admin_' dir.

Daha fazla bilgi için bkz. [Apache ambarı Web Kullanıcı arabirimini kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Parolaları değiştirme

An HDInsight küme iki kullanıcı hesabına sahip olabilir. HDInsight kümesi Kullanıcı hesabı (HTTP Kullanıcı hesabı) ve SSH kullanıcı hesabı, oluşturma işlemi sırasında oluşturulur. Portalı kullanarak, küme kullanıcı hesabı parolasını değiştirebilir ve SSH kullanıcı hesabını değiştirmek için betik eylemleri yapabilirsiniz.

### <a name="change-the-cluster-user-password"></a>Küme kullanıcı parolasını değiştirme

> [!NOTE]  
> Küme kullanıcı (yönetici) parolasının değiştirilmesi, betik eylemlerinin başarısız olması için bu kümede çalışmasına neden olabilir. Çalışan düğümlerini hedefleyen kalıcı betik eylemleriniz varsa, yeniden boyutlandırma işlemleri aracılığıyla kümeye düğüm eklediğinizde bu betikler başarısız olabilir. Betik eylemleri hakkında daha fazla bilgi için bkz. [betik eylemlerini kullanarak HDInsight kümelerini özelleştirme](hdinsight-hadoop-customize-cluster-linux.md).

[Küme giriş sayfasından](#homePage):
1. **Ayarlar**altında **SSH + küme oturumu açma** ' yı seçin.
2. **Kimlik bilgilerini Sıfırla**' yı seçin.
3. Metin kutularına yeni parolayı girin ve onaylayın.
4. **Tamam ' ı**seçin.

Parola, kümedeki tüm düğümlerde değiştirilir.

### <a name="change-the-ssh-user-password"></a>SSH kullanıcı parolasını değiştirme

1. Bir metin düzenleyicisi kullanarak, aşağıdaki metni **ChangePassword.sh**adlı bir dosya olarak kaydedin.

    > [!IMPORTANT]  
    > Bitiş satırı olarak LF kullanan bir düzenleyici kullanmanız gerekir. Düzenleyici CRLF kullanıyorsa, komut dosyası çalışmaz.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. HTTP veya HTTPS adresi kullanılarak dosyayı HDInsight 'tan erişilebilen bir depolama konumuna yükleyin. Örneğin, OneDrive veya Azure Blob depolama gibi bir ortak dosya deposu. URI 'yi (HTTP veya HTTPS adresi), bu URI bir sonraki adımda gerekli olduğundan dosyaya kaydedin.
3. [Küme giriş sayfasında](#homePage), **Ayarlar**' ın altında **betik eylemleri** ' ni seçin.
4. **Betik eylemleri** sayfasında **Yeni Gönder**' i seçin.
5. **Betik eylemini gönder** sayfasında, aşağıdaki bilgileri girin:

   | Alan | Değer |
   | --- | --- |
   | Betik türü | Açılan listeden **özel** ' i seçin.|
   | Name |"SSH parolasını değiştir" |
   | Bash betiği URI 'SI |Changepassword.sh dosyasının URI 'SI |
   | Düğüm türleri: (Head, Worker, Nimbus, Gözetmen, Zookeeper, vb.) |✓ listelenen tüm düğüm türleri için |
   | Parametreler |SSH kullanıcı adını ve ardından yeni parolayı girin. Kullanıcı adı ve parola arasında bir boşluk olmalıdır. |
   | Bu betik eylemini kalıcı yap... |Bu alanı işaretlenmemiş olarak bırakın. |

6. Betiği uygulamak için **Oluştur** ' u seçin. Betik tamamlandıktan sonra, yeni parolayla SSH kullanarak kümeye bağlanabilirsiniz.

## <a name="grantrevoke-access"></a>Erişim verme/iptal etme

HDInsight kümeleri aşağıdaki HTTP Web hizmetlerine sahiptir (Bu hizmetlerin tümü, yeniden oluşan uç noktalara sahiptir):

- ODBC
- JDBC
- Ambarı
- Oozie
- Templeton

Varsayılan olarak, bu hizmetler erişim için verilir. [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access)kullanarak erişimi iptal edebilir/verebilirsiniz.

## <a name="find-the-subscription-id"></a>Abonelik KIMLIĞINI bulun

Her küme bir Azure aboneliğine bağlanır.  Azure abonelik KIMLIĞI, [küme giriş sayfasından](#homePage)görünür.

## <a name="find-the-resource-group"></a>Kaynak grubunu bulma

Azure Resource Manager modunda, her HDInsight kümesi bir Azure Resource Manager grubuyla oluşturulur. Kaynak Yöneticisi grubu, [küme giriş sayfasından](#homePage)görünür.

## <a name="find-the-storage-accounts"></a>Depolama hesaplarını bulma

HDInsight kümeleri, verileri depolamak için bir Azure depolama hesabı veya Azure Data Lake Storage kullanır. Her HDInsight kümesinde bir varsayılan depolama hesabı ve bir dizi bağlı depolama hesabı bulunabilir. Depolama hesaplarını listelemek için, **Ayarlar**altındaki [küme giriş sayfasından](#homePage) **depolama hesapları**' nı seçin.

## <a name="monitor-jobs"></a>İşleri izleme

Bkz. [Apache ambarı Web Kullanıcı arabirimini kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Küme boyutu

Küme [giriş sayfasından](#homePage) **küme boyutu** kutucuğu, bu kümeye ayrılan çekirdek sayısını ve bu kümedeki düğümler için nasıl ayrılacağını gösterir.

> [!IMPORTANT]  
> HDInsight kümesi tarafından sunulan hizmetleri izlemek için, ambarı Web veya ambarı REST API kullanmanız gerekir. Ambarı kullanma hakkında daha fazla bilgi için bkz. [Apache ambarı kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Kümeye Bağlan

- [HDInsight ile Apache Hive kullanma](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bazı temel yönetim işlevlerini öğrendiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure PowerShell kullanarak HDInsight 'ı yönetme](hdinsight-administer-use-powershell.md)
- [Azure CLı kullanarak HDInsight 'ı yönetme](hdinsight-administer-use-command-line.md)
- [HDInsight kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md)
- [Apache ambarı 'nı kullanma hakkında ayrıntılar REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
- [HDInsight 'ta Apache Hive kullanma](hadoop/hdinsight-use-hive.md)
- [HDInsight 'ta Apache Sqoop kullanma](hadoop/hdinsight-use-sqoop.md)
- [HDInsight 'ta Apache Hive ve Apache Pig ile Python Kullanıcı tanımlı Işlevleri (UDF) kullanma](hadoop/python-udf-hdinsight.md)
- [Azure HDInsight 'ta Apache Hadoop sürümü nedir?](hdinsight-component-versioning.md)
