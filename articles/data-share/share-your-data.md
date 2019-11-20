---
title: 'Öğretici: kuruluşunuz dışında paylaşma-Azure veri paylaşımınız'
description: Öğretici-Azure veri paylaşımından yararlanarak müşterilerle ve iş ortaklarıyla veri paylaşma
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 4ef9256404b0d0d4d6379e4f5a76c0d41a38c7cd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499315"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Öğretici: Azure veri paylaşma kullanarak veri paylaşma  

Bu öğreticide, yeni bir Azure veri paylaşımı ayarlamayı ve verilerinizi Azure kuruluşunuzun dışındaki müşteriler ve iş ortaklarıyla paylaşmaya nasıl başlayacağınızı öğreneceksiniz. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Veri paylaşma oluşturun.
> * Veri paylaşımınıza veri kümeleri ekleyin.
> * Veri paylaşımınız için bir eşitleme zamanlaması etkinleştirin. 
> * Veri paylaşımınıza alıcı ekleyin. 

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Alıcının Azure oturum açma e-posta adresi (e-posta diğer adlarını kullanarak çalışmaz).

### <a name="share-from-a-storage-account"></a>Bir depolama hesabından paylaşma:

* Azure depolama hesabı: henüz yoksa bir [Azure depolama hesabı](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) oluşturabilirsiniz
* *Microsoft. Authorization/role atamalar/Write* izninde bulunan depolama hesabına rol ataması ekleme izni. Bu izin sahip rolünde bulunur. 

### <a name="share-from-a-sql-based-source"></a>SQL tabanlı bir kaynaktan paylaşma:

* Paylaşmak istediğiniz tablolar ve görünümler içeren bir Azure SQL veritabanı veya Azure SQL veri ambarı.
* Veri ambarına erişmek için veri paylaşımının izni. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Kendi kendinize sunucu için Azure Active Directory yöneticisi olarak ayarlayın.
    1. Azure Active Directory kullanarak Azure SQL veritabanı/veri ambarına bağlanın.
    1. Veri paylaşımının MSI 'sini db_owner olarak eklemek için aşağıdaki betiği yürütmek üzere sorgu Düzenleyicisi 'ni (Önizleme) kullanın. SQL Server kimlik doğrulaması değil Active Directory kullanarak bağlanmanız gerekir. 
    
```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```                   
*< Share_acc_name >* , veri paylaşma hesabınızın adı olduğunu unutmayın. Henüz bir veri paylaşma hesabı oluşturmadıysanız, bu ön koşul daha sonra geri dönebilirsiniz.  

* İstemci IP SQL Server güvenlik duvarı erişimi: Bu, şu adımlarla yapılabilir: 1. *Güvenlik duvarları ve sanal ağlar 1 '* e gidin. Azure hizmetlerine erişime izin vermek **için Aç düğmesine** tıklayın. 

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-data-share-account"></a>Veri paylaşma hesabı oluşturma

Azure Kaynak grubunda bir Azure veri paylaşma kaynağı oluşturun.

1. Portalın sol üst köşesinde bulunan **Kaynak oluştur** düğmesini (+) seçin.

1. *Veri paylaşımında*arama yapın.

1. Veri paylaşma ' yı seçin ve **Oluştur**' u seçin.

1. Aşağıdaki bilgilerle Azure veri paylaşma kaynağınızın temel ayrıntılarını doldurun. 

     **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Ad | *datashareacount* | Veri paylaşma hesabınız için bir ad belirtin. |
    | Abonelik | Aboneliğiniz | Veri paylaşma hesabınız için kullanmak istediğiniz Azure aboneliğini seçin.|
    | Kaynak grubu | *test-resource-group* | Mevcut bir kaynak grubunu kullanın veya yeni bir kaynak grubu oluşturun. |
    | Konum | *Doğu ABD 2* | Veri paylaşma hesabınız için bir bölge seçin.
    | | |

1. Veri paylaşma hesabınızı sağlamak için **Oluştur** ' u seçin. Yeni bir veri paylaşma hesabının sağlanması genellikle yaklaşık 2 dakika veya daha kısa sürer. 

1. Dağıtım tamamlandığında **Kaynağa Git**' i seçin.

## <a name="create-a-data-share"></a>Veri paylaşma oluşturma

1. Veri paylaşımında genel bakış sayfasına gidin.

    ![Verilerinizi paylaşma](./media/share-receive-data.png "Verilerinizi paylaşma") 

1. **Verilerinizi paylaşmayı Başlat**' ı seçin.

1. **Oluştur**'u seçin.   

1. Veri paylaşımınızın ayrıntılarını doldurun. Bir ad, içerik paylaşma açıklaması ve kullanım koşulları (isteğe bağlı) belirtin. 

    ![EnterShareDetails](./media/enter-share-details.png "Paylaşma ayrıntılarını girin") 

1. **Devam et** 'i seçin

1. Veri paylaşımınıza veri kümeleri eklemek için veri **kümesi Ekle**' yi seçin. 

    ![Veri kümeleri](./media/datasets.png "Veri kümeleri")

1. Eklemek istediğiniz veri kümesi türünü seçin. 

    ![Adddataset 'ler](./media/add-datasets.png "Veri kümesi Ekle")    

1. Paylaşmak istediğiniz nesneye gidin ve ' veri kümesi Ekle ' seçeneğini belirleyin. 

    ![Selectdataset 'ler](./media/select-datasets.png "Veri kümelerini seçin")    

1. Alıcılar sekmesinde, ' + Alıcı Ekle ' seçeneğini belirleyerek veri tüketicinizin e-posta adreslerini girin. 

    ![AddRecipients](./media/add-recipient.png "Alıcı ekleme") 

1. **Devam et** 'i seçin

1. Veri tüketicinizin verilerinizin artımlı güncelleştirmelerini almasını istiyorsanız, anlık görüntü zamanlamasını etkinleştirin. 

    ![EnableSnapshots](./media/enable-snapshots.png "Anlık görüntüleri etkinleştir") 

1. Bir başlangıç saati ve yinelenme aralığı seçin. 

1. **Devam et** 'i seçin

1. Gözden geçir + Oluştur sekmesinde paket Içeriklerinizi, ayarlarınızı, alıcılarını ve eşitleme ayarlarınızı gözden geçirin. **Oluştur**’u seçin

Azure veri paylaşımınız artık oluşturulmuştur ve veri paylaşımınızın alıcısı artık davetinizi kabul etmeye hazırdır. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure veri paylaşımının nasıl oluşturulacağını ve alıcıların nasıl davet alınacağını öğrenirsiniz. Bir veri tüketicisinin bir veri paylaşımının kabul edip alabileceği hakkında bilgi edinmek için, [verileri kabul etme ve alma](subscribe-to-data-share.md) öğreticisine geçin. 
