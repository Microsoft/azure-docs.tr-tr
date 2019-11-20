---
title: Birden çok HDInsight kümesi bir Azure Data Lake Storage hesabı &
description: Tek bir Data Lake Storage hesabıyla birden fazla HDInsight kümesi kullanmayı öğrenin
keywords: HDInsight depolama,,, yapılandırılmış veriler, yapılandırılmamış veriler, Data Lake Store
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: ba0c26d87f2161af514c9430eae5c9949ef92b15
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498196"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Azure Data Lake Storage hesabıyla birden çok HDInsight kümesi kullanma

HDInsight sürüm 3,5 ' den başlayarak, varsayılan dosya sistemi olarak Azure Data Lake Storage hesaplarıyla HDInsight kümeleri oluşturabilirsiniz.
Data Lake Storage, yalnızca büyük miktarlarda veriyi barındırmak için ideal hale getiren sınırsız depolamayı destekler; aynı zamanda tek bir Data Lake Storage hesabını paylaşan birden çok HDInsight kümesini barındırmak için. Depolama olarak Data Lake Storage bir HDInsight kümesi oluşturma hakkında yönergeler için bkz. [hızlı başlangıç: HDInsight 'ta kümeleri ayarlama](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

Bu makale, birden çok **etkin** HDInsight kümesinde kullanılabilecek tek ve paylaşılan bir Data Lake Storage hesabı ayarlamaya yönelik Data Lake Storage yöneticisine öneriler sağlar. Bu öneriler, paylaşılan bir Data Lake Storage hesabında birden çok güvenli ve güvenli olmayan Apache Hadoop kümelerini barındırmak için geçerlidir.


## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage dosya ve klasör düzeyinde ACL 'Ler

Bu makalenin geri kalanında, [Azure Data Lake Storage erişim denetiminde](../data-lake-store/data-lake-store-access-control.md)ayrıntılı olarak açıklanan Azure Data Lake Storage dosya ve klasör düzeyi ACL 'lerinde iyi bir bilgiye sahip olduğunuzu varsaymaktadır.

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Birden çok HDInsight kümesi için Data Lake Storage kurulum
Data Lake Storage hesabıyla birden çok HDInsight kümesi kullanma önerilerini açıklamak için iki düzeyli bir klasör hiyerarşisi getirmemize izin verin. **/Clusters/finans**klasör yapısına sahip bir Data Lake Storage hesabınız olduğunu düşünün. Bu yapıyla, finans organizasyonu için gereken tüm kümeler depolama konumu olarak/Clusters/finans kullanabilir. Daha sonra, başka bir kuruluş, pazarlama söylediğinde aynı Data Lake Storage hesabını kullanarak HDInsight kümeleri oluşturmak istiyorsa,/Clusters/Marketing oluşturamazlar. Şimdilik yalnızca **/Clusters/finans**' ı kullanalım.

Bu klasör yapısını HDInsight kümeleri tarafından etkin bir şekilde kullanılmak üzere etkinleştirmek için, Data Lake Storage Yöneticisi tabloda açıklandığı gibi uygun izinleri atamalıdır. Tabloda gösterilen izinler, varsayılan ACL 'Ler değil, Access-ACL 'Lerine karşılık gelir. 


|Klasör  |İzinler  |Sahip olan kullanıcı  |Sahip olan grup  | Adlandırılmış Kullanıcı | Adlandırılmış Kullanıcı izinleri | Adlandırılmış Grup | Adlandırılmış Grup izinleri |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |Yöneticileri |Yöneticileri  |Hizmet sorumlusu |--x  |FINGRP   |r-x         |
|/kümeler | rwxr-x--x |Yöneticileri |Yöneticileri |Hizmet sorumlusu |--x  |FINGRP |r-x         |
|/Clusters/finans | rwxr-x--t |Yöneticileri |FINGRP  |Hizmet sorumlusu |RWX  |-  |-     |

Tabloda,

- **yönetici** , Data Lake Storage hesabının Oluşturucusu ve yöneticisidir.
- **Hizmet sorumlusu** , hesapla ilişkili Azure ACTIVE DIRECTORY (AAD) hizmet sorumlusu olur.
- **Fingrp** , AAD 'de finans kuruluştan kullanıcıları içeren bir kullanıcı grubudur.

AAD uygulaması oluşturma hakkında yönergeler için (Ayrıca bir hizmet sorumlusu oluşturur) bkz. [AAD uygulaması oluşturma](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). AAD 'de bir Kullanıcı grubu oluşturma yönergeleri için bkz. [Azure Active Directory grupları yönetme](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Göz önünde bulundurmanız gereken bazı önemli noktaları.

- İki düzey klasör yapısı ( **/Clusters/Finance/** ), kümeler için depolama hesabı kullanılmadan **önce** Data Lake Storage yöneticisinin uygun izinlerle oluşturulması ve sağlanması gerekir. Bu yapı, kümeler oluşturulurken otomatik olarak oluşturulmaz.
- Yukarıdaki örnek, **/Clusters/finans** 'ın sahip olduğu grubunu **fingrp** olarak ayarlamayı ve kökten başlayarak tüm klasör hiyerarşisine fingrp için **r-x** erişimine izin sağlamasını önerir. Bu, FINGRP üyelerinin kök 'dan başlayarak klasör yapısına gidebilmesini sağlar.
- Farklı AAD hizmet sorumluları **/Clusters/finans**altında kümeler oluşturabilmesini durumunda, yapışkan bit ( **finans** klasöründe ayarlandığında), bir hizmet sorumlusu tarafından oluşturulan klasörlerin diğer tarafından silinebilmesini sağlar.
- Klasör yapısı ve izinler oluşturulduktan sonra, HDInsight kümesi oluşturma işlemi **/Clusters/Finance/** altında kümeye özgü bir depolama konumu oluşturur. Örneğin, fincluster01 adlı bir küme için depolama alanı **/Clusters/Finance/f, ter01**olabilir. HDInsight kümesi tarafından oluşturulan klasörler için sahiplik ve izinler burada tabloda gösterilmiştir.

    |Klasör  |İzinler  |Sahip olan kullanıcı  |Sahip olan grup  | Adlandırılmış Kullanıcı | Adlandırılmış Kullanıcı izinleri | Adlandırılmış Grup | Adlandırılmış Grup izinleri |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/Clusters/finanace/fincluster01 | rwxr-x---  |Hizmet Sorumlusu |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>İş girişi ve çıkış verileri için öneriler

Bu giriş verilerinin bir işe ve bir iş çıktılarının **/kümelerin**dışında bir klasöre depolanmasını öneririz. Bu, bazı depolama alanını geri kazanmak için kümeye özgü klasör silinse bile, iş girişleri ve çıktılar ileride kullanılmak üzere hala kullanılabilir olur. Böyle bir durumda, iş girişlerini ve çıktılarını depolamak için klasör hiyerarşisinin hizmet sorumlusu için uygun düzeyde erişim sağlamasına izin verildiğinden emin olun.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Tek bir depolama hesabı paylaşan kümeler üzerinde sınırlama

Tek bir Data Lake Storage hesabını paylaşabilen küme sayısı sınırı, bu kümelerde çalıştırılmakta olan iş yüküne bağlıdır. Bir depolama hesabını paylaşan kümeler üzerinde çok fazla küme veya çok ağır iş yükleri olması, depolama hesabı giriş/çıkış 'nin kısıtlanmasına neden olabilir.

## <a name="support-for-default-acls"></a>Varsayılan ACL 'Ler için destek

Adlandırılmış Kullanıcı erişimi (yukarıdaki tabloda gösterildiği gibi) ile bir hizmet sorumlusu oluştururken, adlandırılmış kullanıcıyı varsayılan-ACL ile **eklememeyi** öneririz. Varsayılan ACL 'Ler kullanılarak adlandırılmış Kullanıcı erişiminin sağlanması, sahip olan Kullanıcı, sahip olan grup ve diğerleri için 770 izinlerinin atanmasına neden olur. Bu varsayılan 770 değeri, sahip olan kullanıcının (7) veya sahibi olan grubun (7) izinlerini almaz, diğerleri için tüm izinleri alır (0). Bu, [bilinen sorunlar ve geçici çözümler](#known-issues-and-workarounds) bölümünde ayrıntılı olarak açıklanan belirli bir kullanım durumu ile ilgili bilinen bir soruna neden olur.

## <a name="known-issues-and-workarounds"></a>Bilinen sorunlar ve geçici çözümler

Bu bölümde, Data Lake Storage ile HDInsight kullanımına yönelik bilinen sorunlar ve bunların geçici çözümleri listelenmektedir.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Herkese açık görünür Apache Hadoop YARN kaynakları

Yeni bir Azure Data Lake Storage hesabı oluşturulduğunda, kök dizin, Access-ACL izin bitleri 770 olarak ayarlanan şekilde otomatik olarak sağlanır. Kök klasörün sahibi olan Kullanıcı, hesabı oluşturan kullanıcıya ayarlanır (Data Lake Storage Yöneticisi) ve sahip olan Grup, hesabı oluşturan kullanıcının birincil grubuna ayarlanır. "Diğerleri" için erişim sağlanmaz.

Bu ayarlar, [Yarn 247](https://hwxmonarch.atlassian.net/browse/YARN-247)' de yakalanan belirli bir HDInsight kullanım örneğini etkileyecek şekilde bilinmektedir. İş gönderimleri şuna benzer bir hata iletisiyle başarısız olabilir:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Daha önce bağlı olan Yarn Jira ile bağlantılı olarak, ortak kaynakları yerelleştirirken, yorumdur, uzak dosya sistemindeki izinlerini denetleyerek, istenen tüm kaynakların gerçekten genel olduğunu doğrular. Bu koşula uymayan herhangi bir LocalResource, yerelleştirme için reddedilir. İzinleri denetle, "diğerleri" için dosyaya okuma erişimi içerir. Bu senaryo, Azure Data Lake ' de HDInsight kümeleri barındırırken kullanıma hazır değildir, çünkü Azure Data Lake tüm "diğer" erişimini kök klasör düzeyinde reddeder.

#### <a name="workaround"></a>Geçici çözüm
Yukarıdaki tabloda gösterildiği gibi, örneğin **/** , **/kümeler** ve **/Clusters/finans** gibi **diğer kullanıcılar** için okuma-yürütme izinlerini ayarlayın.

## <a name="see-also"></a>Ayrıca bkz.

* [Hızlı başlangıç: HDInsight'ta kümeleri ayarlama](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma](hdinsight-hadoop-use-data-lake-storage-gen2.md)
