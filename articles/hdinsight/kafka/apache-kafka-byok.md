---
title: Azure HDInsight 'ta Apache Kafka için kendi anahtarınızı getirin
description: Bu makalede, Azure HDInsight üzerinde Apache Kafka depolanan verileri şifrelemek için Azure Key Vault ' den kendi anahtarınızın nasıl kullanılacağı açıklanır.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ba49944011546db45d25cc87c2c4b93c8b99502a
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71122674"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Azure HDInsight 'ta Apache Kafka için kendi anahtarınızı getirin

Azure HDInsight, Apache Kafka için Kendi Anahtarını Getir (BYOK) desteğini içerir. Bu özellik, bekleyen verileri şifrelemek için kullanılan anahtarları sahip olmanızı ve yönetmenizi sağlar.

HDInsight 'taki tüm yönetilen diskler Azure Depolama Hizmeti Şifrelemesi (SSE) ile korunmaktadır. Varsayılan olarak, bu disklerdeki veriler Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir. BYOK ' u etkinleştirirseniz HDInsight 'ın kullanacağı şifreleme anahtarını ve Azure Key Vault kullanarak bunu yöneteceğini sağlarsınız.

BYOK şifrelemesi, küme oluşturma sırasında ek bir maliyet olmadan işlenen tek adımlı bir işlemdir. Yapmanız gereken tek şey, HDInsight 'ı Azure Key Vault ile yönetilen kimlik olarak kaydeder ve kümenizi oluştururken şifreleme anahtarını eklemektir.

Kafka kümesine (Kafka tarafından tutulan çoğaltmalar dahil) yönelik tüm iletiler bir simetrik veri şifreleme anahtarı (DEK) ile şifrelenir. DEK, anahtar kasaınızdan anahtar şifreleme anahtarı (KEK) kullanılarak korunmaktadır. Şifreleme ve şifre çözme işlemleri tamamen Azure HDInsight tarafından işlenir.

Anahtar kasasındaki anahtarları güvenle döndürmek için Azure portal veya Azure CLı kullanabilirsiniz. Bir anahtar döndürülerek HDInsight Kafka kümesi yeni anahtarı dakikalar içinde kullanmaya başlar. Fidye yazılımı senaryolarına ve yanlışlıkla silinmeye karşı korunmak için "geçici silme" anahtar koruma özelliklerini etkinleştirin. Bu koruma özelliği olmadan anahtar kasaları desteklenmez.

## <a name="get-started-with-byok"></a>BYOK ile çalışmaya başlama
BYOK etkin bir Kafka kümesi oluşturmak için aşağıdaki adımları takip edeceğiz:
1. Azure kaynakları için Yönetilen kimlikler oluşturma
2. Azure Key Vault ve anahtarları ayarlama
3. BYOK özellikli HDInsight Kafka kümesi oluşturma
4. Şifreleme anahtarını döndürme

## <a name="create-managed-identities-for-azure-resources"></a>Azure kaynakları için Yönetilen kimlikler oluşturma

   Key Vault kimlik doğrulaması yapmak için, [Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)veya [Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)kullanarak Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun. Yönetilen kimliklerin Azure HDInsight 'ta nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure HDInsight 'Ta Yönetilen kimlikler](../hdinsight-managed-identities.md). Yönetilen kimlikler için Azure Active Directory gerekir ve Kafka için BYOK, Kurumsal Güvenlik Paketi (ESP) bir gereksinim değildir. Key Vault erişim ilkesine eklediğinizde yönetilen kimlik kaynak KIMLIĞINI kaydettiğinizden emin olun.

   ![Azure portal içinde Kullanıcı tarafından atanan yönetilen kimlik oluşturma](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Key Vault ve anahtarları ayarlayın

   HDInsight yalnızca Azure Key Vault destekler. Kendi anahtar kasanıza sahipseniz, anahtarlarınızı Azure Key Vault içine aktarabilirsiniz. Anahtarların "geçici silme" olması gerektiğini unutmayın. "Geçici silme" özelliği REST, .NET/C#, PowerShell ve Azure CLI arabirimleri aracılığıyla kullanılabilir.

   1. Yeni bir Anahtar Kasası oluşturmak için [Azure Key Vault](../../key-vault/key-vault-overview.md) hızlı başlangıcı ' nı izleyin. Mevcut anahtarları içeri aktarma hakkında daha fazla bilgi için [anahtarlar, gizli diziler ve sertifikalar hakkında](../../key-vault/about-keys-secrets-and-certificates.md)bölümünü ziyaret edin.

   2. [Az keykasa Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI komutunu kullanarak anahtar kasasında "geçici silme" özelliğini etkinleştirin.

        ```Azure CLI
        az keyvault update --name <Key Vault Name> --enable-soft-delete
        ```

   3. Anahtar oluşturma

        a. Yeni bir anahtar oluşturmak için **Ayarlar**altındaki **anahtarlar** menüsünden **Oluştur/içeri aktar** ' ı seçin.

        ![Azure Key Vault yeni bir anahtar oluştur](./media/apache-kafka-byok/kafka-create-new-key.png "Azure Key Vault yeni bir anahtar oluştur")

        b. Oluşturma **seçeneklerini** belirleyin ve anahtara bir ad verin.

        ![Apache Kafka anahtar adı oluştur](./media/apache-kafka-byok/apache-kafka-create-key.png "Anahtar adı oluştur")

        c. Anahtar listesinden oluşturduğunuz anahtarı seçin.

        ![Apache Kafka Anahtar Kasası anahtar listesi](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. Kafka küme şifrelemesi için kendi anahtarınızı kullandığınızda, anahtar URI 'sini sağlamanız gerekir. **Anahtar tanımlayıcısını** kopyalayın ve kümenizi oluşturmaya hazır olana kadar bir yere kaydedin.

        ![Apache Kafka get anahtar tanımlayıcısı](./media/apache-kafka-byok/kafka-get-key-identifier.png)

    4. Yönetilen kimliği Anahtar Kasası erişim ilkesine ekleyin.

        a. Yeni bir Azure Key Vault erişim ilkesi oluşturun.

        ![Yeni Azure Key Vault erişim ilkesi oluştur](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. **Asıl seçin**altında oluşturduğunuz Kullanıcı tarafından atanan yönetilen kimliği seçin.

        ![Azure Key Vault erişim ilkesi için sorumlusu seçin](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. **Anahtar ve** **sarmalama**anahtarını **almak**için **anahtar izinleri** ayarlayın.

        ![Azure Key Vault Access policy1 için anahtar Izinleri ayarlama](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Azure Key Vault Access policy1 için anahtar Izinleri ayarlama")

        d. **Get**, **set**ve **Delete**için **gizli izinleri** ayarlayın.

        ![Azure Key Vault Access policy2 için anahtar Izinleri ayarlama](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Azure Key Vault Access policy2 için anahtar Izinleri ayarlama")

        e. **Kaydet**'e tıklayın. 

        ![Azure Key Vault erişim ilkesini Kaydet](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>HDInsight kümesi oluşturma

   Artık yeni bir HDInsight kümesi oluşturmaya hazırsınız. BYOK, yalnızca küme oluşturma sırasında yeni kümelere uygulanabilir. Şifreleme BYOK kümelerinden kaldırılamaz ve var olan kümelere BYOK eklenemez.

   ![Azure portal Kafka disk şifrelemesi](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   Küme oluşturma sırasında, anahtar sürümü de dahil olmak üzere tam anahtar URL 'sini sağlayın. Örneğin, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Ayrıca, yönetilen kimliği kümeye atamanız ve anahtar URI 'sini sağlamanız gerekir.

## <a name="rotating-the-encryption-key"></a>Şifreleme anahtarını döndürme

   Oluşturulduktan sonra Kafka kümesi tarafından kullanılan şifreleme anahtarlarını değiştirmek isteyebileceğiniz senaryolar olabilir. Bu, Portal aracılığıyla kolayca olabilir. Bu işlem için, kümenin hem geçerli anahtara hem de hedeflenen yeni anahtara erişimi olması gerekir, aksi takdirde anahtar döndürme işlemi başarısız olur.

   Anahtarı döndürmek için, yeni anahtarın tam URL 'sine sahip olmanız gerekir (bkz. [Key Vault ve anahtarlarını kurulumun](#setup-the-key-vault-and-keys)3. adımında). Bunu yaptıktan sonra, portalda Kafka küme özellikleri bölümüne gidin ve **disk şifreleme anahtarı URL 'si**altında **anahtarı Değiştir** ' e tıklayın. Yeni anahtar URL 'sini girin ve anahtarı döndürmek için Gönder ' i yazın.

   ![Kafka döndürme disk şifreleme anahtarı](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>BYOK Apache Kafka için SSS

**Kafka kümesi anahtar kasayma nasıl erişir?**

   Küme oluşturma sırasında bir yönetilen kimliği HDInsight Kafka kümesiyle ilişkilendirin. Bu yönetilen kimlik, küme oluşturma işleminden önce veya sırasında oluşturulabilir. Ayrıca, anahtarın depolandığı anahtar kasasına yönetilen kimlik erişimi vermeniz gerekir.

**Bu özellik HDInsight 'ta tüm Kafka kümeleri için kullanılabilir mi?**

   BYOK şifrelemesi yalnızca Kafka 1,1 ve üzeri kümeler için mümkündür.

**Farklı konular/bölümler için farklı anahtarlara sahip olabilir miyim?**

   Hayır, kümedeki tüm yönetilen diskler aynı anahtarla şifrelenir.

**Küme, anahtar kasasına veya anahtara erişimi kaybederse ne olur?**
Küme anahtara erişimi kaybederse, uyarılar Apache ambarı portalında gösterilir. Bu durumda, **anahtar değiştirme** işlemi başarısız olur. Anahtar erişimi geri yüklendikten sonra, ambarı uyarıları kaldırılır ve anahtar döndürme işlemi başarılı bir şekilde gerçekleştirilebilir.

   ![Apache Kafka anahtar erişim ambarı uyarısı](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**Anahtarlar siliniyorsa kümeyi nasıl kurtarabilirim?**

   Yalnızca "geçici silme" özelliği desteklendiğinden, anahtarlar anahtar kasasında kurtarılıyorsa, küme anahtarlara yeniden erişim elde etmelidir. Azure Key Vault anahtarını kurtarmak için, bkz. [Undo-Azkeyvaultkeykaldırması](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) veya [az-keykasa-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Aynı anda bir BYOK kümesi ve BYOK kümesi ile çalışan üretici/tüketici uygulamalarına sahip olabilir miyim?**

   Evet. BYOK kullanımı, Producer/Consumer uygulamaları için saydamdır. Şifreleme, işletim sistemi katmanında gerçekleşir. Mevcut üretici/tüketici Kafka uygulamalarında hiçbir değişiklik yapılması gerekmez.

**İşletim sistemi diskleri/kaynak diskleri de şifrelenir mi?**

   Hayır. İşletim sistemi diskleri ve kaynak diskleri şifrelenmez.

**Bir küme yukarı ölçeklendirilirse, yeni aracılar sorunsuz bir şekilde destek alacak mı?**

   Evet. Kümenin, ölçek artırma sırasında anahtar kasasındaki anahtara erişmesi gerekir. Kümedeki tüm yönetilen diskleri şifrelemek için aynı anahtar kullanılır.

**BYOK, konumumdaki kullanılabilir mi?**

   Kafka BYOK, tüm genel bulutlarda kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault](../../key-vault/key-vault-overview.md)nedir?
* Azure Key Vault kullanmaya başlamak için bkz. [Azure Key Vault kullanmaya](../../key-vault/key-vault-overview.md)başlama.
