---
title: "Güvenlik: şirket içi Apache Hadoop Azure HDInsight 'a geçirme"
description: Şirket içi Hadoop kümelerini Azure HDInsight 'a geçirmeye yönelik güvenlik ve DevOps en iyi uygulamalarını öğrenin.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 1cebe425e323eefda6e26b0f32ddeda0118a70d1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494965"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Şirket içi Apache Hadoop kümelerini Azure HDInsight 'a geçirme-Security ve DevOps en iyi uygulamaları

Bu makale, Azure HDInsight sistemlerinde güvenlik ve DevOps için öneriler sağlar. Şirket içi Apache Hadoop sistemlerini Azure HDInsight 'a geçirmeye yardımcı olmak için en iyi uygulamaları sağlayan bir serinin bir parçasıdır.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Kurumsal Güvenlik Paketi ile küme güvenliğini sağlama ve yönetme

Kurumsal Güvenlik Paketi (ESP) Active Directory tabanlı kimlik doğrulamasını, çok kullanıcılı desteği ve rol tabanlı erişim denetimini destekler. ESP seçeneği seçili olduğunda, HDInsight kümesi Active Directory etki alanına katılır ve kuruluş yöneticisi, Apache Ranger kullanarak Apache Hive güvenliği için rol tabanlı erişim denetimi 'ni (RBAC) yapılandırabilir. Yönetici ayrıca çalışanlara göre veri erişimini ve erişim denetimi ilkelerine yapılan değişiklikleri denetleyebilir.

Şu küme türlerinde ESP kullanılabilir: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka ve etkileşimli sorgu (Hive LLAP). 

Etki alanına katılmış HDInsight kümesini dağıtmak için aşağıdaki adımları kullanın:

- Etki alanı adını geçirerek Azure Active Directory (AAD) dağıtın.
- Azure Active Directory Domain Services Dağıtma (AAD DS).
- Gerekli sanal ağı ve alt ağı oluşturun.
- AAD DS 'yi yönetmek için sanal ağda bir VM dağıtın.
- VM 'yi etki alanına ekleyin.
- AD ve DNS araçları 'nı yükler.
- AAD DS yöneticisinin bir kuruluş birimi (OU) oluşturmasını sağlayabilirsiniz.
- AAD 'ler için LDAPS 'yi etkinleştirin.
- Azure Active Directory ' de bir hizmet hesabı oluşturun ve bu sayede, OU 'ya temsilci okuma & yazma yönetici izniyle izin verebilirsiniz. Bu hizmet hesabı daha sonra makineleri etki alanına katabilir ve makine sorumlularını OU 'ya yerleştirebilir. Ayrıca, küme oluşturma sırasında belirttiğiniz OU içinde hizmet sorumluları oluşturabilir.


    > [!Note]
    > Hizmet hesabının AD etki alanı yönetici hesabı olması gerekmez.


- Aşağıdaki parametreleri ayarlayarak HDInsight ESP kümesini dağıtın:
    - **Etki alanı adı**: Azure AD DS ilişkili etki alanı adı.
    - **Etki alanı Kullanıcı adı**: önceki bölümde oluşturduğunuz Azure AD DS DC tarafından yönetilen etki alanındaki hizmet hesabı, örneğin: `hdiadmin@contoso.onmicrosoft.com`. Bu etki alanı kullanıcısı, bu HDInsight kümesinin Yöneticisi olacak.
    - **Etki alanı parolası**: hizmet hesabının parolası.
    - **Kuruluş birimi**: HDInsight kümesiyle kullanmak istediğiniz OU 'nun ayırt edici adı, örneğin: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Bu OU yoksa, HDInsight kümesi hizmet hesabının ayrıcalıklarını kullanarak OU oluşturmayı dener.
    - **LDAPS URL 'si**: örneğin, `ldaps://contoso.onmicrosoft.com:636`.
    - **Erişim Kullanıcı grubu**: kullanıcıları kümeyle eşitlemek istediğiniz güvenlik grupları, örneğin: `HiveUsers`. Birden çok kullanıcı grubu belirtmek istiyorsanız, bunları noktalı virgül '; ' ile ayırın. ESP kümesi oluşturulmadan önce, gruplar dizinde bulunmalıdır.

Daha fazla bilgi için aşağıdaki makalelere bakın:

- [Etki alanına katılmış HDInsight kümeleriyle güvenliği Apache Hadoop bir giriş](../domain-joined/hdinsight-security-overview.md)

- [HDInsight 'ta Azure etki alanına katılmış Apache Hadoop kümeleri planlayın](../domain-joined/apache-domain-joined-architecture.md)
- [Azure Active Directory Domain Services kullanarak etki alanına katılmış HDInsight kümesi yapılandırma](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Azure Active Directory kullanıcılarını HDInsight kümesiyle eşitler](../hdinsight-sync-aad-users-to-cluster.md)
- [Etki alanına katılmış HDInsight 'ta Apache Hive ilkeleri yapılandırma](../domain-joined/apache-domain-joined-run-hive.md)
- [Etki alanına katılmış HDInsight Hadoop kümelerinde Apache Oozie Çalıştır](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Uçtan uca kurumsal güvenliği uygulama

Aşağıdaki denetimler kullanılarak uçtan uca kurumsal güvenlik elde edilebilir:

- **Özel ve korunan veri işlem hattı (çevre düzeyi güvenliği)**
    - Çevre düzeyi güvenlik, Azure sanal ağları, ağ güvenlik grupları ve ağ geçidi hizmeti aracılığıyla elde edilebilir.

- **Veri erişimi için kimlik doğrulama ve yetkilendirme**
    - Azure Active Directory Domain Services kullanarak etki alanına katılmış HDInsight kümesi oluşturun. (Kurumsal Güvenlik Paketi).
    - AD kullanıcılarına yönelik küme kaynaklarına rol tabanlı erişim sağlamak için ambarı 'nı kullanın.
    - Tablo/sütun/satır düzeyinde Hive için erişim denetim ilkeleri ayarlamak için Apache Ranger kullanın.
    - Kümeye SSH erişimi yalnızca yönetici ile kısıtlanabilir.

- **Denetim**
    - HDInsight kümesi kaynaklarına ve verilerine yönelik tüm erişimi görüntüleyin ve rapor edin.
    - Erişim denetimi ilkelerine yapılan tüm değişiklikleri görüntüleyin ve rapor edin.

- **Şifreleme**
    - Microsoft tarafından yönetilen anahtarları veya müşteri tarafından yönetilen anahtarları kullanarak saydam sunucu tarafı şifreleme.
    - Istemci tarafı şifreleme, https ve TLS kullanan geçiş şifrelemesi.

Daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure sanal ağlarına genel bakış](../../virtual-network/virtual-networks-overview.md)
- [Azure ağ güvenlik gruplarına genel bakış](../../virtual-network/security-overview.md)
- [Azure sanal ağ eşlemesi](../../virtual-network/virtual-network-peering-overview.md)
- [Azure depolama Güvenlik Kılavuzu](../../storage/common/storage-security-guide.md)
- [Bekleyen Azure Depolama Hizmeti Şifrelemesi](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>İzleme & uyarma kullanma

Daha fazla bilgi için şu makaleye bakın:

[Azure İzleyiciye Genel Bakış](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Küme yükseltme

En yeni özelliklerden yararlanmak için düzenli olarak en son HDInsight sürümüne yükseltin. Aşağıdaki adımlar, kümeyi en son sürüme yükseltmek için kullanılabilir:

1. En son kullanılabilir HDInsight sürümünü kullanarak yeni bir TEST HDInsight kümesi oluşturun.
1. İşlerin ve iş yüklerinin beklendiği gibi çalıştığından emin olmak için yeni kümede test edin.
1. İşleri veya uygulamaları veya iş yüklerini gereken şekilde değiştirin.
1. Küme düğümlerinde yerel olarak depolanan tüm geçici verileri yedekleyin.
1. Mevcut kümeyi silin.
1. Önceki kümeyle aynı varsayılan verileri ve meta depolamayı kullanarak, aynı VNET alt ağında en son HDInsight sürümünün bir kümesini oluşturun.
1. Yedeklenen tüm geçici verileri içeri aktarın.
1. Yeni kümeyi kullanarak işleri başlatın/işleme devam edin.

Daha fazla bilgi için bkz: [HDInsight kümesini yeni bir sürüme yükseltme](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Küme işletim sistemlerini düzeltme eki uygulama

Yönetilen bir Hadoop hizmeti olarak HDInsight, HDInsight kümeleri tarafından kullanılan VM 'lerin işletim sisteminde düzeltme eki uygulamayı üstlenir.

Daha fazla bilgi için bkz. [HDInsight Için Işletim sistemi düzeltme eki uygulama](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Geçiş sonrası

1. **Uygulamaları** düzeltme-tekrarlayarak işler, işlemler ve betiklerinizde gerekli değişiklikleri yapın.
2. **Testleri gerçekleştirin** -işlev ve performans testlerini tekrarlayarak çalıştırın.
3. **En uygun** performans sorunlarını yukarıdaki test sonuçlarına göre çözün ve ardından performans iyileştirmelerini onaylamak için yeniden test edin.

## <a name="next-steps"></a>Sonraki adımlar

- [Hdınsight 4,0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction)hakkında daha fazla bilgi edinin.
