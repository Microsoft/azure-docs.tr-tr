---
title: Azure portal kullanarak MariaDB sunucusu için Azure veritabanı oluşturma ve yönetme
description: Bu makalede, Azure portalını kullanarak hızlı bir şekilde MariaDB sunucusu için yeni bir Azure veritabanı oluşturma ve sunucuyu yönetme işlemlerinin nasıl yapılacağı açıklanır.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/09/2019
ms.openlocfilehash: 5aae3eb0582956ccb45cc41d8400f489f1077bad
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143446"
---
# <a name="create-and-manage-azure-database-for-mariadb-server-using-azure-portal"></a>Azure portal kullanarak MariaDB sunucusu için Azure veritabanı oluşturma ve yönetme
Bu konu başlığı altında, MariaDB sunucusu için nasıl hızlı bir şekilde yeni bir Azure veritabanı oluşturabileceğiniz açıklanır. Ayrıca, Azure portal kullanarak sunucunun nasıl yönetileceği hakkındaki bilgileri de içerir. Sunucu Yönetimi sunucu ayrıntılarını ve veritabanlarını görüntülemeyi, parolayı sıfırlamayı, kaynakları ölçeklendirmeyi ve sunucuyu silmeyi içerir.

## <a name="log-in-to-the-azure-portal"></a>Azure portalında oturum açma
[Azure Portal](https://portal.azure.com)’da oturum açın.

## <a name="create-an-azure-database-for-mariadb-server"></a>MariaDB için Azure Veritabanı sunucusu oluşturma
"Mydemoserver" adlı bir MariaDB sunucusu için Azure veritabanı oluşturmak üzere aşağıdaki adımları izleyin.

1. Azure portal sol üst köşesinde bulunan **kaynak oluştur** düğmesine tıklayın.

2. Yeni sayfada **veritabanları**' nı ve ardından veritabanları sayfasında, **MariaDB için Azure veritabanı**' nı seçin.

    > MariaDB sunucusu için Azure veritabanı, tanımlı bir dizi [işlem ve depolama](./concepts-pricing-tiers.md) kaynağı ile oluşturulur. Veritabanı bir Azure Kaynak grubu içinde ve MariaDB sunucusu için Azure veritabanı 'nda oluşturulur.

   ![Yeni-sunucu oluştur](./media/howto-create-manage-server-portal/create-new-server.png)

3. Aşağıdaki bilgileri kullanarak MariaDB için Azure veritabanı formunu doldurun:

    | **Form Alanı** | **Alan Açıklaması** |
    |----------------|-----------------------|
    | *Sunucu adı* | demosunucum (sunucu adı genel olarak benzersiz) |
    | *Abonelik* | mysubscription (açılan menüden seçim yapın) |
    | *Kaynak grubu* | myresourcegroup (yeni bir kaynak grubu oluşturun veya mevcut olanı kullanın) |
    | *Kaynak seçme* | Boş (boş bir MariaDB sunucusu oluşturun) |
    | *Sunucu yöneticisi oturum açma bilgileri* | myadmin (yönetici hesabı adını ayarlayın) |
    | *Parola* | Yönetici hesabı parolasını ayarla |
    | *Parolayı onayla* | yönetici hesabı parolasını onaylayın |
    | *Location* | Güneydoğu Asya (Kuzey Avrupa ve Batı ABD arasında seçim yapın) |
    | *Sürüm* | 10,3 (MariaDB sunucu sürümü için Azure veritabanı 'nı seçin) |

   ![Yeni-sunucu oluştur](./media/howto-create-manage-server-portal/form-field.png)

4. Yeni sunucunuzun hizmet katmanını ve performans düzeyini belirtmek için **sunucuyu Yapılandır** ' a tıklayın. **Genel amaçlı** sekmesini seçin. *Gen 5*, *4 sanal çekırdek*, *100 GB*ve *7 gün* , **Işlem oluşturma**, **sanal çekirdek**, **depolama**ve **yedekleme saklama süresi**için varsayılan değerlerdir. Bu kaydırıcıları olduğu gibi bırakabilirsiniz. Coğrafi olarak yedekli depolamada sunucu yedeklerinizi etkinleştirmek için, **Fazladan Yedek Seçenekleri**’nde **Coğrafi Olarak Yedeklemeli**’yi seçin.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. İnceleme ekranına gitmek ve tüm ayrıntıları doğrulamak için **gözden geçir + oluştur** ' a tıklayın. Sunucuyu sağlamak için **Oluştur**’a tıklayın. Sağlama birkaç dakika sürer.

    > Dağıtımlarınızın kolayca izlenmesini sağlamak için **panoya sabitle** seçeneğini belirleyin.

## <a name="update-an-azure-database-for-mariadb-server"></a>MariaDB sunucusu için Azure veritabanını güncelleştirme
Yeni sunucu sağlandıktan sonra, Kullanıcı, yönetici parolasını sıfırlama, fiyatlandırma katmanını değiştirme ve sanal çekirdek ya da depolamayı değiştirerek sunucu ölçeğini büyütme veya azaltma dahil olmak üzere var olan sunucuyu yapılandırmaya yönelik çeşitli seçeneklere sahiptir.

### <a name="change-the-administrator-user-password"></a>Yönetici Kullanıcı parolasını değiştirme
1. Sunucu **genel bakışta**parola sıfırlama penceresini göstermek Için **Parolayı Sıfırla** ' ya tıklayın.

   ![genel bakış](./media/howto-create-manage-server-portal/overview.png)

2. Yeni bir parola girin ve penceredeki parolayı gösterildiği gibi onaylayın:

   ![sıfırlama-parola](./media/howto-create-manage-server-portal/reset-password.png)

3. Yeni parolayı kaydetmek için **Tamam** ' ı tıklatın.

### <a name="change-the-pricing-tier"></a>Fiyatlandırma katmanını değiştirme
> [!NOTE]
> Ölçeklendirme yalnızca bellek için Iyileştirilmiş hizmet katmanlarına Genel Amaçlı desteklenir, tersi de geçerlidir. Lütfen sunucu oluşturulduktan sonra, MariaDB için Azure veritabanı 'nda sunucu oluşturma işleminden sonra temel fiyatlandırma katmanında değişiklik yapmanız gerektiğini unutmayın.
> 
1. **Ayarlar**' ın altında bulunan **fiyatlandırma katmanı**' na tıklayın.
2. Değiştirmek istediğiniz **fiyatlandırma katmanını** seçin.

    ![değişiklik-Fiyatlandırma Katmanı](./media/howto-create-manage-server-portal/change-pricing-tier.png)

4. Değişiklikleri kaydetmek için **Tamam**'a tıklayın. 

### <a name="scale-vcores-updown"></a>Sanal çekirdekleri yukarı/aşağı ölçeklendirme

1. **Ayarlar**' ın altında bulunan **fiyatlandırma katmanı**' na tıklayın.

2. Kaydırıcıyı istediğiniz değere taşıyarak **Vcore** ayarını değiştirin.

    ![ölçek-işlem](./media/howto-create-manage-server-portal/scale-compute.png)

3. Değişiklikleri kaydetmek için **Tamam**'a tıklayın.

### <a name="scale-storage-up"></a>Depolama ölçeğini artırma

1. **Ayarlar**' ın altında bulunan **fiyatlandırma katmanı**' na tıklayın.

2. Kaydırıcıyı istediğiniz değere taşıyarak **depolama** ayarını değiştirin.

    ![ölçek depolama](./media/howto-create-manage-server-portal/scale-storage.png)

3. Değişiklikleri kaydetmek için **Tamam**'a tıklayın.

## <a name="delete-an-azure-database-for-mariadb-server"></a>MariaDB sunucusu için Azure veritabanı 'nı silme

1. Sunucuya **genel bakış**' da Sil ' e tıklayarak silme onaylama istemi ' ni açın.

    ![sil](./media/howto-create-manage-server-portal/delete.png)

2. Çift onay için giriş kutusuna sunucunun adını yazın.

    ![Onayla-Sil](./media/howto-create-manage-server-portal/confirm.png)

3. Sunucu silmeyi onaylamak için **Sil** düğmesine tıklayın. Bildirim çubuğunda "MariaDB sunucusu başarıyla silindi" açılır penceresini bekleyin.

## <a name="list-the-azure-database-for-mariadb-databases"></a>MariaDB veritabanları için Azure veritabanı 'nı listeleyin
Sunucu **genel bakışta**, alt kısımdaki veritabanı kutucuğunu görene kadar aşağı kaydırın. Sunucudaki tüm veritabanları tabloda listelenir.

   ![veritabanlarını göster](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mariadb-server"></a>MariaDB sunucusu için Azure veritabanı 'nın ayrıntılarını gösterme
Sunucu hakkındaki ayrıntılı bilgileri görüntülemek için **Ayarlar** ' ın altında bulunan **Özellikler**' e tıklayın.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Sonraki adımlar

[Hızlı Başlangıç: Azure portal kullanarak MariaDB sunucusu için Azure veritabanı oluşturma](./quickstart-create-mariadb-server-database-using-azure-portal.md)