---
title: Içeri/dışarı aktarma hizmeti için müşteri tarafından yönetilen anahtarları yapılandırmak üzere Azure portal kullanın
description: Azure Içeri/dışarı aktarma hizmeti için Azure Key Vault ile müşteri tarafından yönetilen anahtarları yapılandırmak üzere Azure portal nasıl kullanacağınızı öğrenin. Müşteri tarafından yönetilen anahtarlar, erişim denetimleri oluşturmanıza, döndürmenize, devre dışı bırakmanızı ve iptal edebilmesini sağlar.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 71c322385c9b70eb46e1c478c1322752ee4561e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706757"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Içeri/dışarı aktarma hizmeti için Azure Key Vault 'de müşteri tarafından yönetilen anahtarları kullanın

Azure Içeri/dışarı aktarma, sürücüleri bir şifreleme anahtarı aracılığıyla kilitlemek için kullanılan BitLocker anahtarlarını korur. Varsayılan olarak, BitLocker anahtarları Microsoft tarafından yönetilen anahtarlarla şifrelenir. Şifreleme anahtarları üzerinde ek denetim için, müşteri tarafından yönetilen anahtarlar da sağlayabilirsiniz.

Müşteri tarafından yönetilen anahtarların bir Azure Key Vault oluşturulması ve depolanması gerekir. Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault nedir?](../key-vault/general/overview.md)

Bu makalede, müşteri tarafından yönetilen anahtarların [Azure Portal](https://portal.azure.com/)Içeri/dışarı aktarma hizmeti ile nasıl kullanılacağı gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce şunlardan emin olun:

1. İçindeki yönergelere göre bir içeri veya dışarı aktarma işi oluşturdunuz:

    - [Bloblar için bir içeri aktarma Işi oluşturun](storage-import-export-data-to-blobs.md).
    - [Dosyalar için bir içeri aktarma Işi oluşturun](storage-import-export-data-to-files.md).
    - [Blob 'lar için dışarı aktarma işi oluşturma](storage-import-export-data-from-blobs.md)

2. İçinde, BitLocker anahtarınızı korumak için kullanabileceğiniz bir anahtara sahip mevcut bir Azure Key Vault vardır. Azure portal kullanarak bir Anahtar Kasası oluşturmayı öğrenmek için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Key Vault oluşturma](../key-vault/general/quick-create-portal.md).

    - **Geçici silme** ve **temizleme** , mevcut Key Vault ayarlanır. Bu özellikler varsayılan olarak etkinleştirilmemiştir. Bu özellikleri etkinleştirmek için, aşağıdaki makalelerden birinde **geçici silme özelliğini etkinleştirme** ve **Temizleme korumasını etkinleştirme** başlıklı bölümlere bakın:

        - [PowerShell ile geçici silme nasıl kullanılır](../key-vault/general/key-vault-recovery.md).
        - [CLI ile geçici silme nasıl kullanılır](../key-vault/general/key-vault-recovery.md).
    - Mevcut Anahtar Kasası 2048 boyutunda bir RSA anahtarına veya daha fazlasına sahip olmalıdır. Anahtarlar hakkında daha fazla bilgi için bkz. [anahtarlar hakkında](../key-vault/keys/about-keys.md).
    - Anahtar Kasası, verilerinizin depolama hesabıyla aynı bölgede olmalıdır.  
    - Mevcut bir Azure Key Vault yoksa, aşağıdaki bölümde açıklandığı gibi satır içi de oluşturabilirsiniz.

## <a name="enable-keys"></a>Anahtarları etkinleştir

Içeri/dışarı aktarma hizmetiniz için müşteri tarafından yönetilen anahtarı yapılandırmak isteğe bağlıdır. Varsayılan olarak, Içeri/dışarı aktarma hizmeti, BitLocker anahtarınızı korumak için Microsoft tarafından yönetilen bir anahtar kullanır. Azure portal müşteri tarafından yönetilen anahtarları etkinleştirmek için şu adımları izleyin:

1. Içeri aktarma işiniz için **genel bakış** dikey penceresine gidin.
2. Sağ bölmede, **BitLocker anahtarlarınızın nasıl şifrelendiğini** seçin seçeneğini belirleyin.

    ![Şifreleme seçeneğini belirleyin](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. **Şifreleme** dikey penceresinde, cihaz BitLocker anahtarını görüntüleyebilir ve kopyalayabilirsiniz. **Şifreleme türü** altında, BitLocker anahtarınızı nasıl korumak istediğinizi seçebilirsiniz. Varsayılan olarak, Microsoft tarafından yönetilen anahtar kullanılır.

    ![BitLocker anahtarını görüntüle](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Müşteri tarafından yönetilen anahtar belirtme seçeneğiniz vardır. Müşteri tarafından yönetilen anahtarı seçtikten sonra **Anahtar Kasası ve bir anahtar seçin**.

    ![Müşteri tarafından yönetilen anahtar seçin](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. **Azure Key Vault anahtarı seç** dikey penceresinde, abonelik otomatik olarak doldurulur. **Anahtar Kasası** için, açılan listeden var olan bir anahtar kasasını seçebilirsiniz.

    ![Azure Key Vault seçin veya oluşturun](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Yeni bir Anahtar Kasası oluşturmak için **Yeni oluştur** ' u de seçebilirsiniz. **Anahtar Kasası oluştur dikey** penceresinde, kaynak grubunu ve Anahtar Kasası adını girin. Diğer tüm varsayılanları kabul edin. **Gözden geçir + Oluştur**’u seçin.

    ![Yeni Azure Key Vault oluştur](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Anahtar kasanız ile ilişkili bilgileri gözden geçirin ve **Oluştur**' u seçin. Anahtar Kasası oluşturma işleminin tamamlanabilmesi için birkaç dakika bekleyin.

    ![Azure Key Vault oluştur](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. **Azure Key Vault anahtarı seç**' de, varolan anahtar kasasında bir anahtar seçebilirsiniz.

9. Yeni bir Anahtar Kasası oluşturduysanız, anahtar oluşturmak için **Yeni oluştur** ' u seçin. RSA anahtar boyutu 2048 veya daha büyük olabilir.

    ![Azure Key Vault yeni anahtar oluştur](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Anahtar kasasını oluştururken geçici silme ve Temizleme koruması etkinleştirilmemişse, Anahtar Kasası, geçici silme ve Temizleme koruması etkin olacak şekilde güncelleştirilir.

10. Anahtarınız için ad belirtin, diğer varsayılanları kabul edin ve **Oluştur**' u seçin.

    ![Yeni anahtar oluştur](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. **Sürümü** seçin ve ardından **Seç**' i seçin. Anahtar Kasanızda bir anahtarın oluşturulduğunu size bildirirsiniz.

    ![Anahtar Kasası 'nda yeni anahtar oluşturuldu](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

**Şifreleme** dikey penceresinde, anahtar kasasını ve müşterinizin yönettiği anahtar için seçilen anahtarı görebilirsiniz.

> [!IMPORTANT]
> Yalnızca Microsoft tarafından yönetilen anahtarları devre dışı bırakabilir ve içeri/dışarı aktarma işinin herhangi bir aşamasında müşterinin yönetilen anahtarlarına geçebilirsiniz. Ancak, bir kez oluşturduktan sonra müşterinin yönettiği anahtarı devre dışı bırakabilirsiniz.

## <a name="troubleshoot-customer-managed-key-errors"></a>Müşterinin yönettiği anahtar hatalarında sorun giderme

Müşteri tarafından yönetilen anahtarınızla ilgili herhangi bir hata alırsanız, sorunlarını gidermek için aşağıdaki tabloyu kullanın:

| Hata kodu     |Ayrıntılar     | Gider?    |
|----------------|------------|-----------------|
| Cmkerroraccessiptal edildi | Müşterinin yönettiği anahtara erişim iptal edilir.                                                       | Evet, aşağıdakileri denetle: <ol><li>Anahtar Kasası hala MSI 'ye erişim ilkesinde sahiptir.</li><li>Erişim ilkesinde al, sarmalama ve sarmalama izinleri etkin.</li><li>Anahtar Kasası güvenlik duvarının arkasındaki bir VNet 'daysanız, **Microsoft güvenilir Hizmetleri 'Ne Izin ver** ' in etkinleştirilip etkinleştirilmediğini denetleyin.</li><li>İş kaynağının MSI `None` 'Ların API 'ler kullanılarak sıfırlanıp sıfırlandığını denetleyin.<br>Evet ise değeri olarak ayarlayın `Identity = SystemAssigned` . Bu işlem, iş kaynağının kimliğini yeniden oluşturur.<br>Yeni kimlik oluşturulduktan sonra `Get` `Wrap` `Unwrap` anahtar kasasının erişim ilkesinde yeni kimlik için,, ve izinlerini etkinleştirin</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | Müşteri tarafından yönetilen anahtar devre dışı bırakıldı.                                         | Evet, anahtar sürümünü etkinleştirerek     |
| CmkErrorKeyNotFound      | Müşteri tarafından yönetilen anahtar bulunamıyor. | Evet, anahtar silinmişse, ancak temizleme süresi içinde ise, [Anahtar Kasası anahtarını kaldırma Işlemini geri al özelliğini](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)kullanarak devam eder.<br>Değilse <ol><li>Evet, müşteri anahtarı yedekledi ve geri yüklerse.</li><li>Hayır, aksi durumda.</li></ol>
| CmkErrorVaultNotFound |Müşterinin yönettiği anahtarın anahtar kasası bulunamıyor. |   Anahtar Kasası silinmişse:<ol><li>Evet, temizleme koruma süresi içinde ise, [anahtar kasasını kurtarma](../key-vault/general/soft-delete-overview.md#key-vault-recovery)bölümündeki adımları kullanarak.</li><li>Temizleme koruma süresinin ötesinde, hayır.</li></ol><br>Diğer bir deyişle, Anahtar Kasası farklı bir kiracıya geçirildiyse, aşağıdaki adımlardan biri kullanılarak kurtarılabilir:<ol><li>Anahtar kasasını eski kiracıya geri döndürür.</li><li>`Identity = None`Değerini ayarlayın ve ardından değerine geri ayarlayın `Identity = SystemAssigned` . Bu, yeni kimlik oluşturulduktan sonra kimliği siler ve yeniden oluşturur. `Get` `Wrap` `Unwrap` Anahtar kasasının erişim ilkesindeki yeni kimlik için, ve izinlerini etkinleştirin.</li></ol>|

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault nedir](../key-vault/general/overview.md)?