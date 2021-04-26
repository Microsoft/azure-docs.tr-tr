---
title: Taramalar için kimlik bilgileri oluşturma ve yönetme
description: Azure purview 'da kimlik bilgileri oluşturma ve yönetme adımları hakkında bilgi edinin.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 3802d25ebd8f21ab5b8991a66ceb6650f2f276a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461717"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Azure purview 'da kaynak kimlik doğrulaması için kimlik bilgileri

Bu makalede, Azure purview 'da kimlik bilgileri oluşturma açıklanır. Bu kayıtlı kimlik bilgileri, kayıtlı kimlik doğrulama bilgilerini hızlı bir şekilde yeniden kullanmanıza ve veri kaynağı taramalarınız için uygulamanıza

## <a name="prerequisites"></a>Önkoşullar

- Azure Anahtar Kasası. Bir oluşturma hakkında bilgi edinmek için bkz. [hızlı başlangıç: Azure Portal kullanarak bir Anahtar Kasası oluşturma](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Giriş

Kimlik bilgileri, Azure purview 'ın kayıtlı veri kaynaklarınızda kimlik doğrulamak için kullanabileceği kimlik doğrulama bilgileridir. Kullanıcı adı/parola gerektiren temel kimlik doğrulaması gibi çeşitli kimlik doğrulama senaryoları için bir kimlik bilgisi nesnesi oluşturulabilir. Kimlik bilgileri, seçilen kimlik doğrulama yöntemine göre kimlik doğrulaması yapmak için gereken belirli bilgileri yakalar. Kimlik bilgileri, kimlik bilgileri oluşturma işlemi sırasında hassas kimlik doğrulama bilgilerini almak için mevcut Azure Anahtar Kasası sırlarınızı kullanır.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Taramaları ayarlamak için purview yönetilen kimliğini kullanın

Taramaları ayarlamak için purview yönetilen kimliğini kullanıyorsanız, bu kimlik bilgilerini açık bir şekilde oluşturmanız ve anahtar kasanızın, bunları depolamak için takip görünümüne bağlamanız gerekmez. Veri kaynaklarınızı taramak için adım adım yönetilen kimliği ekleme hakkında ayrıntılı yönergeler için aşağıdaki veri kaynağına özgü kimlik doğrulama bölümlerine bakın:

- [Azure Blob Depolama](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage 2. Nesil](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Veritabanı](register-scan-azure-sql-database.md)
- [Azure SQL veritabanı yönetilen örneği](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure purview hesabınızda Azure Anahtar Kasası bağlantıları oluşturma

Bir kimlik bilgisi oluşturabilmeniz için önce mevcut Azure Key Vault örneklerinden bir veya daha fazlasını Azure purview hesabınızla ilişkilendirin.

1. [Azure Portal](https://portal.azure.com)Azure purview hesabınızı seçin ve Azure purview Studio 'yu açın. Azure purview Studio 'daki **yönetim merkezine** gidin ve **kimlik bilgileri**' ne gidin.

2. **Kimlik bilgileri** sayfasında **Yönet Key Vault bağlantıları**' nı seçin.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Azure Key Vault bağlantılarını yönetme":::

3. Key Vault bağlantılarını Yönet sayfasından **+ Yeni** ' yi seçin.

4. Gerekli bilgileri girip **Oluştur**' u seçin.

5. Aşağıdaki örnekte gösterildiği gibi Key Vault Azure purview hesabınızla başarıyla ilişkilendirildiğini doğrulayın:

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Onaylamak için Azure Key Vault bağlantılarını görüntüleyin.":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Azure Key Vault yönetilen kimlik erişimine izin verin

1. Azure Key Vault gidin.

2. **Erişim ilkeleri** sayfasını seçin.

3. **Erişim Ilkesi Ekle**' yi seçin.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Bu MSI 'yi AKV 'ye Ekle":::

4. Gizli dizi **izinleri** açılır listesinde, **Al** ve **Listele** izinleri ' ni seçin.

5. **Asıl seçin** için, yönetilen kimlik ' i seçin. Purview örnek adını **veya** yönetilen KIMLIK uygulama kimliğini kullanarak PURVIEW MSI için arama yapabilirsiniz. Şu anda bileşik kimlikleri desteklemiyoruz (yönetilen kimlik adı + uygulama KIMLIĞI).

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Erişim İlkesi Ekle":::

6. **Add (Ekle)** seçeneğini belirleyin.

7. Erişim ilkesini kaydetmek için **Kaydet** ' i seçin.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Erişim ilkesini Kaydet":::

## <a name="create-a-new-credential"></a>Yeni kimlik bilgisi oluştur

Bu kimlik bilgisi türleri purview 'da desteklenir:

- Temel kimlik doğrulaması: **parolayı** Anahtar Kasası 'nda gizli dizi olarak eklersiniz.
- Hizmet sorumlusu: **hizmet sorumlusu anahtarını** Anahtar Kasası 'nda gizli dizi olarak eklersiniz.
- SQL kimlik doğrulaması: **parolayı** Anahtar Kasası 'nda gizli dizi olarak eklersiniz.
- Hesap anahtarı: **hesap anahtarını** Anahtar Kasası 'nda gizli dizi olarak eklersiniz.
- Rol ARN: bir Amazon S3 veri kaynağı Için, AWS 'de **rolünüzü** ekleyin. 

Daha fazla bilgi için bkz. [Key Vault bir gizli dizi ekleme](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) ve [purview için yeni bir AWS rolü oluşturma](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview).

Gizli dizileri anahtar kasasında sakladıktan sonra:

1. Azure purview ' de kimlik bilgileri sayfasına gidin.

2. **+ Yeni** seçeneğini belirleyerek yeni kimlik bilgilerinizi oluşturun.

3. Gerekli bilgileri sağlayın. **Kimlik doğrulama yöntemini** ve bir gizli anahtar seçilecek **Key Vault bağlantıyı** seçin.

4. Tüm ayrıntılar doldurulduktan sonra **Oluştur**' u seçin.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Yeni kimlik bilgisi":::

5. Yeni kimlik bilgilerinizin liste görünümünde görüntülendiğini ve kullanıma hazırlandiğini doğrulayın.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Kimlik bilgisini görüntüle":::

## <a name="manage-your-key-vault-connections"></a>Anahtar Kasası bağlantılarınızı yönetin

1. Ada göre Key Vault bağlantıları arama/bulma

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Anahtar kasasında ara":::

2. Bir veya daha fazla Key Vault bağlantıyı silme

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Anahtar kasasını Sil":::

## <a name="manage-your-credentials"></a>Kimlik bilgilerinizi yönetin

1. Kimlik bilgilerini ada göre arayın/bulun.
  
2. Mevcut bir kimlik bilgisi seçin ve güncelleştirmeleri yapın.

3. Bir veya daha fazla kimlik bilgisini silin.

## <a name="next-steps"></a>Sonraki adımlar

[Tarama kuralı kümesi oluşturma](create-a-scan-rule-set.md)
