---
title: Kuruluşunuz dışında paylaşma (Azure portal)-Azure veri paylaşımının hızlı başlangıç
description: Bu hızlı başlangıçta Azure veri paylaşma kullanarak müşteriler ve iş ortakları ile veri paylaşmayı öğrenin.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 10/30/2020
ms.openlocfilehash: 1442720fdf48aaa7da76e181b168a04306ff3e33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96186465"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>Hızlı başlangıç: Azure portal Azure veri paylaşımından kullanarak veri paylaşma

Bu hızlı başlangıçta, Azure portal kullanarak depolama hesabından veri paylaşmak üzere yeni bir Azure veri paylaşımının nasıl ayarlanacağını öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Alıcının Azure oturum açma e-posta adresi (e-posta diğer adlarını kullanarak çalışmaz).
* Kaynak Azure veri deposu, veri paylaşma kaynağı oluşturmak için kullanacağınız sunucudan farklı bir Azure aboneliğinde yer alıyorsa, [Microsoft. DataShare kaynak sağlayıcısını](concepts-roles-permissions.md#resource-provider-registration) Azure veri deposunun bulunduğu abonelikte kaydedin. 

### <a name="share-from-a-storage-account"></a>Depolama hesabından paylaşma

* Azure depolama hesabı: henüz yoksa bir [Azure depolama hesabı](../storage/common/storage-account-create.md) oluşturabilirsiniz
* *Microsoft. Storage/storageAccounts/Write* içinde bulunan depolama hesabına yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır.
* *Microsoft. Authorization/role atamalar/Write*' de bulunan depolama hesabına rol ataması ekleme izni. Bu izin **Sahip** rolünde vardır. 

## <a name="create-a-data-share-account"></a>Veri paylaşma hesabı oluşturma

Azure Kaynak grubunda bir Azure veri paylaşma kaynağı oluşturun.

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Portalın sol üst köşesinde bulunan **kaynak oluştur** düğmesini (+) seçin.

1. *Veri paylaşımında* arama yapın.

1. **Veri paylaşma** ' yı seçin ve **Oluştur**' u seçin.

1. Aşağıdaki bilgilerle Azure veri paylaşma kaynağınızın temel ayrıntılarını doldurun. 

   **Ayar** | **Önerilen değer** | **Alan açıklaması**
   |---|---|---|
   | Abonelik | Aboneliğiniz | Veri paylaşma hesabınız için kullanmak istediğiniz Azure aboneliğini seçin.|
   | Kaynak grubu | *test-resource-group* | Mevcut bir kaynak grubunu kullanın veya yeni bir kaynak grubu oluşturun. |
   | Konum | *Doğu ABD 2* | Veri paylaşma hesabınız için bir bölge seçin.
   | Name | *datashareaccount* | Veri paylaşma hesabınız için bir ad belirtin. |

1. Veri paylaşma hesabınızı sağlamak için **gözden geçir + oluştur**' u seçin ve **Oluştur** ' a tıklayın. Yeni bir veri paylaşma hesabının sağlanması genellikle yaklaşık 2 dakika veya daha kısa sürer.

1. Dağıtım tamamlandığında **Kaynağa Git**' i seçin.

## <a name="create-a-share"></a>Bir paylaşma oluşturun

1. Veri paylaşımında genel bakış sayfasına gidin.

   ![Verilerinizi paylaşma](./media/share-receive-data.png "Verilerinizi paylaşma") 

1. **Verilerinizi paylaşmayı Başlat**' ı seçin.

1. **Oluştur**’u seçin.

1. Paylaşımınızın ayrıntılarını doldurun. Bir ad, paylaşma türü, içerik paylaşma açıklaması ve kullanım koşulları (isteğe bağlı) belirtin. 

   ![EnterShareDetails](./media/enter-share-details.png "Paylaşma ayrıntılarını girin") 

1. **Devam**’ı seçin.

1. Paylaşımınıza veri kümeleri eklemek için **veri kümesi Ekle**' yi seçin. 

   ![Paylaşımınıza veri kümeleri ekleme](./media/datasets.png "Veri kümeleri")

1. Eklemek istediğiniz veri kümesi türünü seçin. Önceki adımda seçtiğiniz paylaşma türüne (anlık görüntü veya yerinde) bağlı olarak farklı bir veri kümesi türleri listesi görürsünüz. 

   ![Adddataset 'ler](./media/add-datasets.png "Veri kümesi Ekle")    

1. Paylaşmak istediğiniz nesneye gidin ve ' veri kümesi Ekle ' seçeneğini belirleyin. 

   ![Selectdataset 'ler](./media/select-datasets.png "Veri kümelerini seçin")    

1. Alıcılar sekmesinde, ' + Alıcı Ekle ' seçeneğini belirleyerek veri tüketicinizin e-posta adreslerini girin.

   ![AddRecipients](./media/add-recipient.png "Alıcı ekleme") 

1. **Devam**’ı seçin.

1. Anlık görüntü paylaşma türü ' nu seçtiyseniz, veri tüketicisine verilerinizin güncelleştirmelerini sağlamak için anlık görüntü zamanlamasını yapılandırabilirsiniz. 

   ![EnableSnapshots](./media/enable-snapshots.png "Anlık görüntüleri etkinleştir") 

1. Bir başlangıç saati ve yinelenme aralığı seçin. 

1. **Devam**’ı seçin.

1. Gözden geçir + Oluştur sekmesinde paket Içeriklerinizi, ayarlarınızı, alıcılarını ve eşitleme ayarlarınızı gözden geçirin. **Oluştur**’u seçin.

Azure veri paylaşımınız artık oluşturulmuştur ve veri paylaşımınızın alıcısı artık davetinizi kabul etmeye hazırdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak artık gerekli olmadığında, **veri paylaşımının genel bakış** sayfasına gidin ve **Sil** ' i seçerek kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure veri paylaşımının nasıl oluşturulacağını öğrendiniz. Bir veri tüketicisinin bir veri paylaşımının kabul edip alabileceği hakkında bilgi edinmek için, [verileri kabul etme ve alma](subscribe-to-data-share.md) öğreticisine geçin. 
