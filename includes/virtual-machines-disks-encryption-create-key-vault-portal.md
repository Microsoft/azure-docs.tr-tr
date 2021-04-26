---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "88815479"
---
Diskleriniz için müşteri tarafından yönetilen anahtarların kurulması, ilk kez yapıyorsanız, belirli bir sırada kaynak oluşturmanızı gerektirir. İlk olarak, bir Azure Key Vault oluşturmanız ve ayarlamanız gerekecektir.

## <a name="set-up-your-azure-key-vault"></a>Azure Key Vault ayarlama

1. [Azure portal](https://aka.ms/diskencryptionupdates) oturum açın.
1. **Anahtar kasaları** arayın ve seçin.

    [![Arama iletişim kutusu genişletilmiş Azure portal ekran görüntüsü.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Azure anahtar kasanızın, disk şifreleme kümesi, VM, diskler ve anlık görüntülerinin tümünün başarılı olması için aynı bölgede ve aboneliğin olması gerekir.

1. Yeni bir Key Vault oluşturmak için **+ Ekle** ' yi seçin.
1. Yeni bir kaynak grubu oluşturma.
1. Bir Anahtar Kasası adı girin, bir bölge seçin ve bir fiyatlandırma katmanı seçin.

    > [!NOTE]
    > Key Vault örneğini oluştururken, geçici silme ve Temizleme korumasını etkinleştirmeniz gerekir. Geçici silme, Key Vault belirli bir bekletme süresi (90 gün varsayılanı) için silinen bir anahtar bulundurmasını sağlar. Temizleme koruması, silinen bir anahtarın saklama süresi boşalıncaya kadar kalıcı olarak silinememesini sağlar. Bu ayarlar yanlışlıkla silme nedeniyle verileri kaybetmenize karşı korur. Bu ayarlar, yönetilen diskleri şifrelemek için bir Key Vault kullanılırken zorunludur.

1. **Gözden geçir + oluştur**' u seçin, seçimlerinizi doğrulayın ve **Oluştur**' u seçin.

    ![Azure Key Vault oluşturma deneyiminin ekran görüntüsü. Oluşturduğunuz belirli değerleri gösterme](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Anahtar kasanızın dağıtımı tamamlandıktan sonra, bunu seçin.
1. **Ayarlar** altında **anahtarlar** ' ı seçin.
1. **Oluştur/Içeri aktar**' ı seçin.

    ![Key Vault kaynak ayarları bölmesinin ekran görüntüsü. Ayarlar içinde oluştur/Al düğmesini gösterir.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Her iki **anahtar türünü** **RSA** ve **RSA anahtar boyutu** **2048** olarak ayarlayın.
1. Kalan seçimleri istediğiniz gibi doldurup **Oluştur**' u seçin.

    ![Oluştur/Al düğmesine bir kez görüntülenen anahtar oluştur dikey penceresinin ekran görüntüsü seçilir](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>Disk şifreleme kümesini ayarlama

1. **Disk şifreleme kümelerini** arayın ve seçin.
1. **Disk şifreleme kümeleri** dikey penceresinde **+ Ekle**' yi seçin.

    ![Disk şifreleme portalı ana ekranının ekran görüntüsü. Ekle düğmesini vurgulama](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Kaynak grubunuzu seçin, şifreleme kümesini adlandırın ve anahtar kasanız ile aynı bölgeyi seçin.
1. **Şifreleme türü** için **müşteri tarafından yönetilen bir anahtarla Rest 'i** seçin.

    > [!NOTE]
    > Belirli bir şifreleme türüyle bir disk şifreleme kümesi oluşturduktan sonra, bu değişiklik değiştirilemez. Farklı bir şifreleme türü kullanmak istiyorsanız, yeni bir disk şifreleme kümesi oluşturmanız gerekir.

1. **Bir anahtar seçmek Için tıklayın ' ı** seçin.
1. Daha önce oluşturduğunuz anahtar kasasını ve anahtarı ve sürümü seçin.
1. **Seç**' e basın.
1. **Gözden Geçir ve Oluştur**’u ve sonra **Oluştur**’u seçin.

    ![Disk şifrelemesi oluşturma dikey penceresinin ekran görüntüsü. Abonelik, kaynak grubu, disk şifreleme kümesi adı, bölge ve Anahtar Kasası + anahtar Seçicisi gösteriliyor.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. Oluşturma tamamlandıktan sonra disk şifrelemesi kümesini açın ve açılan uyarıyı seçin.

    ![Uyarı açılır penceresi: ' disk, resim veya anlık görüntüyü bir disk şifrelemesi kümesiyle ilişkilendirmek Için, Anahtar Kasası 'na izin vermeniz gerekir. Devam etmek için bu uyarıyı seçin](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    İki bildirim açılır ve başarılı olmalıdır. Bu, anahtar kasanızın bulunduğu disk şifreleme kümesini kullanmanıza olanak sağlar.

    ![Anahtar kasanızın başarılı izninin ve rol atamasının ekran görüntüsü.](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)