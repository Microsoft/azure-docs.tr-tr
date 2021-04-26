---
title: Veri şifreleme-Azure portal-PostgreSQL için Azure veritabanı-tek sunucu
description: Azure portal kullanarak PostgreSQL için Azure veritabanı için veri şifrelemeyi ayarlamayı ve yönetmeyi öğrenin.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 98c413f85fe556f5fb413716037163931753e1d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240741"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Azure portal kullanarak PostgreSQL için Azure veritabanı için veri şifreleme tek sunucu

PostgreSQL için Azure veritabanı tek sunucu için veri şifrelemeyi ayarlamak ve yönetmek üzere Azure portal nasıl kullanacağınızı öğrenin.

## <a name="prerequisites-for-azure-cli"></a>Azure CLı önkoşulları

* Bu abonelikte bir Azure aboneliğiniz olması ve bir yönetici olmanız gerekir.
* Azure Key Vault, müşteri tarafından yönetilen anahtar için kullanılacak bir anahtar kasası ve anahtar oluşturun.
* Anahtar Kasası, müşteri tarafından yönetilen anahtar olarak kullanmak için aşağıdaki özelliklere sahip olmalıdır:
  * [Geçici silme](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Korumalı Temizleme](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Müşteri tarafından yönetilen anahtar olarak kullanılabilmesi için anahtarın aşağıdaki özniteliklere sahip olması gerekir:
  * Son kullanma tarihi yok
  * Devre dışı bırakılmadı
  * Al, sarmalama tuşu ve sarmalama anahtar işlemlerini gerçekleştirebiliyor

## <a name="set-the-right-permissions-for-key-operations"></a>Anahtar işlemleri için doğru izinleri ayarla

1. Key Vault ' de erişim **ilkeleri**  >  **erişim ilkesi Ekle**' yi seçin.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="Erişim ilkeleriyle Key Vault ekran görüntüsü ve erişim Ilkesi vurgulandı":::

2. **Anahtar izinleri**' ni seçin ve PostgreSQL sunucusunun adı olan Al, **sarmalama**, **geri** **Al** ve **asıl**' ı seçin. Sunucu sorumlunuz mevcut sorumlular listesinde bulunamazsa, kaydolmanız gerekir. Veri şifrelemeyi ilk kez ayarlamaya çalıştığınızda sunucu sorumlunuzu kaydetmeniz istenir ve başarısız olur.  

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="Erişim ilkesine genel bakış":::

3. **Kaydet**’i seçin.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>PostgreSQL için Azure veritabanı için veri şifrelemeyi ayarlama tek sunucu

1. PostgreSQL için Azure veritabanı 'nda, müşteri tarafından yönetilen anahtarı ayarlamak için **veri şifreleme** ' yi seçin.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="Veri şifrelemesi vurgulanmış şekilde PostgreSQL için Azure veritabanı ekran görüntüsü":::

2. Bir anahtar kasası ve anahtar çifti seçebilir ya da bir anahtar tanımlayıcı girebilirsiniz.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="Veri şifreleme seçenekleri vurgulanmış şekilde PostgreSQL için Azure veritabanı ekran görüntüsü":::

3. **Kaydet**’i seçin.

4. Tüm dosyaların (geçici dosyalar dahil) tamamen şifrelendiğinden emin olmak için sunucuyu yeniden başlatın.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Geri yükleme veya çoğaltma sunucuları için veri şifrelemeyi kullanma

PostgreSQL için Azure veritabanı tek sunucu, Key Vault ' de depolanan bir müşterinin yönetilen anahtarıyla şifrelendikten sonra, sunucunun yeni oluşturulan kopyası da şifrelenir. Bu yeni kopyayı yerel veya coğrafi geri yükleme işlemi aracılığıyla ya da bir çoğaltma (yerel/bölge) işlemi aracılığıyla yapabilirsiniz. Bu nedenle, şifrelenmiş bir PostgreSQL sunucusu için, şifrelenmiş geri yüklenmiş bir sunucu oluşturmak için aşağıdaki adımları kullanabilirsiniz.

1. Sunucunuzda **genel bakış**  >  **geri yükleme**' yi seçin.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="PostgreSQL için Azure veritabanı 'Na genel bakış ve geri yükleme vurgulanmış ekran görüntüsü":::

   Ya da çoğaltma özellikli bir sunucu için, **Ayarlar** başlığı altında **çoğaltma**' yı seçin.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-replica.png" alt-text="Çoğaltma vurgulanmış şekilde PostgreSQL için Azure veritabanı ekran görüntüsü":::

2. Geri yükleme işlemi tamamlandıktan sonra oluşturulan yeni sunucu birincil sunucunun anahtarıyla şifrelenir. Ancak, sunucudaki Özellikler ve seçenekler devre dışı bırakılır ve sunucuya erişilemez. Bu, tüm veri düzenlemesini engeller, çünkü yeni sunucu kimliği henüz anahtar kasasına erişmek için izin verilmemiş.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="PostgreSQL için Azure veritabanı 'nın, erişilemeyen durum vurgulandığı ekran görüntüsü":::

3. Sunucuyu erişilebilir hale getirmek için geri yüklenen sunucuda anahtarı yeniden doğrulayın. **Veri şifrelemeyi**  >  **yeniden doğrula anahtarını** seçin.

   > [!NOTE]
   > Yeni sunucunun hizmet sorumlusunun anahtar kasasına erişim izni verilmesi gerektiğinden, ilk yeniden doğrulama denemesi başarısız olur. Hizmet sorumlusunu oluşturmak için **anahtarı yeniden doğrula**' yı seçin, bu, bir hatayı gösterir, ancak hizmet sorumlusu oluşturur. Bundan sonra bu makalede daha önce bahsedilen [adımlara](#set-the-right-permissions-for-key-operations) bakın.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="Yeniden doğrulama adımı vurgulanmış şekilde PostgreSQL için Azure veritabanı 'nın ekran görüntüsü":::

   Yeni sunucuya Anahtar Kasası erişimi sağlamanız gerekir.

4. Hizmet sorumlusu kaydedildikten sonra anahtarı yeniden doğruladıktan sonra sunucu normal işlevselliğini sürdürür.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="PostgreSQL için Azure veritabanı 'nın, geri yüklenen işlevselliği gösteren ekran görüntüsü":::

## <a name="next-steps"></a>Sonraki adımlar

 Veri şifreleme hakkında daha fazla bilgi edinmek için bkz. [PostgreSQL Için Azure veritabanı istemci tarafından yönetilen anahtarla tek sunuculu veri şifreleme](concepts-data-encryption-postgresql.md).
