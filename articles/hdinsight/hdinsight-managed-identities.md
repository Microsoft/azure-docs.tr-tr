---
title: Azure HDInsight 'ta Yönetilen kimlikler
description: Azure HDInsight 'ta yönetilen kimliklerin uygulanmasına genel bir bakış sağlar.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: f2b7f6e8421a735db131bc05605936e8cb2d87eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944124"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight 'ta Yönetilen kimlikler

Yönetilen kimlik, kimlik bilgileri Azure tarafından yönetilen Azure Active Directory (Azure AD) ' de kayıtlı bir kimliktir. Yönetilen kimliklerle, hizmet sorumlularını Azure AD 'ye kaydetmeniz gerekmez. Ya da sertifikalar gibi kimlik bilgilerini saklayın.

Yönetilen kimlikler Azure AD etki alanı hizmetlerine erişmek veya gerektiğinde Azure Data Lake Storage 2. dosyalara erişmek için Azure HDInsight 'ta kullanılır.

İki tür yönetilen kimlik vardır: Kullanıcı tarafından atanan ve sistem tarafından atanan. Azure HDInsight yalnızca Kullanıcı tarafından atanan yönetilen kimlikleri destekler. HDInsight, sistem tarafından atanan yönetilen kimlikleri desteklemez. Kullanıcı tarafından atanan yönetilen kimlik, tek başına bir Azure kaynağı olarak oluşturulur. Bu, daha sonra bir veya daha fazla Azure hizmet örneğine atayabilirsiniz. Buna karşılık, Azure AD 'de sistem tarafından atanan yönetilen bir kimlik oluşturulur ve ardından doğrudan belirli bir Azure Hizmeti örneğinde otomatik olarak etkinleştirilir. Bu durumda, sistem tarafından atanan yönetilen kimliğin yaşam süresi daha sonra etkinleştirilmiş olan hizmet örneği ömrü ile bağlantılıdır.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight Yönetilen kimlik uygulama

Azure HDInsight 'ta Yönetilen kimlikler yalnızca, iç bileşenler için HDInsight hizmeti tarafından kullanılabilir. Şu anda, dış hizmetlere erişim için HDInsight küme düğümlerinde yüklü yönetilen kimlikleri kullanarak erişim belirteçleri oluşturmak için desteklenen bir yöntem yoktur. İşlem VM 'Leri gibi bazı Azure hizmetlerinde, Yönetilen kimlikler erişim belirteçleri almak için kullanabileceğiniz bir uç nokta ile uygulanır. Bu uç nokta, HDInsight düğümlerinde Şu anda kullanılamıyor.

Analiz işlerine (örn. SCALA işleri) gizli dizileri/parolalar yerleştirmekten kaçınmak için uygulamalarınızı önyüklemeniz gerekiyorsa, betik eylemlerini kullanarak kendi sertifikalarınızı küme düğümlerine dağıtabilir ve ardından bir erişim belirteci almak için bu sertifikayı kullanabilirsiniz (örneğin, Azure Keykasasına erişim için).

## <a name="create-a-managed-identity"></a>Yönetilen kimlik oluşturma

Yönetilen kimlikler aşağıdaki yöntemlerden biriyle oluşturulabilir:

* [Azure portalındaki](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Yönetilen kimliği yapılandırmaya yönelik kalan adımlar, kullanılacağı senaryoya bağlıdır.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight 'ta yönetilen kimlik senaryoları

Yönetilen kimlikler, Azure HDInsight 'ta birden çok senaryoda kullanılır. Ayrıntılı kurulum ve yapılandırma yönergeleri için bkz. ilgili belgeler:

* [Azure Data Lake Storage 2. Nesil](hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#create-a-user-assigned-managed-identity)
* [Kurumsal Güvenlik Paketi](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Müşteri tarafından yönetilen anahtar disk şifrelemesi](disk-encryption.md)

HDInsight, bu senaryolar için kullandığınız yönetilen kimliklerin sertifikalarını otomatik olarak yenileyecek. Ancak, uzun süre çalışan kümeler için birden çok farklı yönetilen kimlik kullanıldığında bir sınırlama vardır. sertifika yenilemesi, tüm yönetilen kimlikler için beklendiği gibi çalışmayabilir. Bu sınırlama nedeniyle, uzun süre çalışan kümeler kullanmayı planlıyorsunuz (örn. 60 günden fazla), yukarıdaki tüm senaryolar için aynı yönetilen kimliği kullanmanızı öneririz. 

Birden çok farklı yönetilen kimliğe sahip uzun süre çalışan bir küme oluşturduysanız ve bu sorunlardan birinde çalışıyorsa:
 * ESP kümelerinde, küme hizmetleri başarısız oluyor veya ölçeği yukarı ve diğer işlemler kimlik doğrulama hatalarıyla başarısız oluyor.
 * ESP kümelerinde, AAD-DS LDAPS sertifikasını değiştirirken, LDAPS sertifikası otomatik olarak güncellenmez ve bu nedenle LDAP eşitleme ve ölçek UPS başarısız olmaya başlar.
 * ADLS 2. için MSI erişimi başarısız olmaya başladı.
 * Şifreleme anahtarları CMK senaryosunda döndürülemez.

daha sonra, yukarıdaki senaryolar için gerekli rolleri ve izinleri kümede kullanılan tüm yönetilen kimliklere atamanız gerekir. Örneğin, ADLS 2. ve ESP kümelerinde farklı Yönetilen kimlikler kullandıysanız, bu sorunlara karşı çalışmayı önlemek için, her iki durumda da "Depolama Blobu veri sahibi" ve "HDInsight etki alanı Hizmetleri katılımcısı" rollerinin atanmış olması gerekir.

## <a name="faq"></a>SSS

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Küme oluşturulduktan sonra yönetilen kimliği silersem ne olur?

Yönetilen kimlik gerektiğinde kümeniz sorun olarak çalışır. Şu anda küme oluşturulduktan sonra yönetilen bir kimliği güncelleştirmek veya değiştirmek mümkün değildir. Bu nedenle, küme çalışma zamanı sırasında yönetilen kimliğin silinmediğinden emin olmak için önerimiz. Ya da kümeyi yeniden oluşturabilir ve yeni bir yönetilen kimlik atayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure kaynakları için yönetilen kimlikler nedir?](../active-directory/managed-identities-azure-resources/overview.md)
