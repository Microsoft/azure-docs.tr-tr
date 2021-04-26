---
title: 'Öğretici: kuruluşunuz dışında paylaşma-Azure veri paylaşımınız'
description: Öğretici-Azure veri paylaşımından yararlanarak müşterilerle ve iş ortaklarıyla veri paylaşma
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: b8d49e3b3c6f6407fe241e00ada5039bd94fd706
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870886"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Öğretici: Azure Veri Paylaşımı’nı kullanarak verileri paylaşma  

Bu öğreticide, yeni bir Azure veri paylaşımı ayarlamayı ve verilerinizi Azure kuruluşunuzun dışındaki müşteriler ve iş ortaklarıyla paylaşmaya nasıl başlayacağınızı öğreneceksiniz. 

Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * Veri Paylaşımı oluşturun.
> * Veri Paylaşımınıza veri kümelerini ekleyin.
> * Veri paylaşımınız için bir anlık görüntü zamanlaması etkinleştirin. 
> * Veri Paylaşımınıza alıcıları ekleyin. 

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Alıcının Azure oturum açma e-posta adresi (e-posta diğer adlarını kullanarak çalışmaz).
* Kaynak Azure veri deposu, veri paylaşma kaynağı oluşturmak için kullanacağınız sunucudan farklı bir Azure aboneliğinde yer alıyorsa, [Microsoft. DataShare kaynak sağlayıcısını](concepts-roles-permissions.md#resource-provider-registration) Azure veri deposunun bulunduğu abonelikte kaydedin. 

### <a name="share-from-a-storage-account"></a>Depolama hesabından paylaşma

* Azure depolama hesabı: henüz yoksa bir [Azure depolama hesabı](../storage/common/storage-account-create.md) oluşturabilirsiniz
* *Microsoft. Storage/storageAccounts/Write* içinde bulunan depolama hesabına yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır.
* *Microsoft. Authorization/role atamalar/Write*' de bulunan depolama hesabına rol ataması ekleme izni. Bu izin **Sahip** rolünde vardır. 


### <a name="share-from-a-sql-based-source"></a>SQL tabanlı bir kaynaktan paylaşma
SQL kaynağından veri paylaşmaya yönelik önkoşulların listesi aşağıda verilmiştir. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Azure SQL veritabanı veya Azure SYNAPSE Analytics 'ten (eski adıyla Azure SQL DW) paylaşım önkoşulları

* Paylaşmak istediğiniz tablolar ve görünümler içeren bir Azure SQL veritabanı veya Azure SYNAPSE Analytics (eski adıyla Azure SQL DW).
* *Microsoft. SQL/Servers/veritabanları/Write*'TA bulunan SQL Server 'da veritabanlarına yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır.
* SQL Server **Azure Active Directory Yöneticisi**
* Güvenlik duvarı erişimi SQL Server. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal, SQL Server 'a gidin. Sol gezinmede *güvenlik duvarları ve sanal ağlar ' ı* seçin.
    1. *Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin vermek* için **Evet** ' i tıklatın.
    1. **+ İstemci IP 'Si Ekle**' ye tıklayın. İstemci IP adresi değişebilir. Bu işlemin bir sonraki Azure portal SQL verilerini paylaşışınızda tekrarlanması gerekebilir. Ayrıca, bir IP aralığı ekleyebilirsiniz.
    1. **Kaydet**’e tıklayın. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Azure SYNAPSE Analytics (çalışma alanı) SQL havuzundan paylaşım önkoşulları

* * Bir Azure SYNAPSE Analytics (çalışma alanı) paylaşmak istediğiniz tablolarla ayrılmış bir SQL Havuzu. Görünüm paylaşımı Şu anda desteklenmiyor. Sunucusuz SQL havuzundan paylaşım Şu anda desteklenmiyor.
* *Microsoft. SYNAPSE/Workspaces/Sqlhavuzlar/Write* Içinde bulunan SYNAPSE çalışma alanındaki SQL havuzuna yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır.
* SYNAPSE çalışma alanı SQL havuzuna erişmek için veri paylaşımının yönetilen kimliği izni. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal ' de SYNAPSE çalışma alanı ' na gidin. Sol gezinmede SQL Active Directory Yöneticisi ' ni seçin ve kendiniz **Azure Active Directory Yöneticisi** olarak ayarlayın.
    1. SYNAPSE Studio 'yu açın, sol gezinmede *Yönet* ' i seçin. Güvenlik altında *erişim denetimi* ' ni seçin. Kendinize **SQL Yöneticisi** veya **çalışma alanı yönetici** rolü atayın.
    1. SYNAPSE Studio 'da sol gezinmeden *Geliştir* ' i seçin. Veri paylaşımının kaynak yönetimli kimliğini bir db_datareader olarak eklemek için SQL havuzunda aşağıdaki betiği yürütün. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       *<share_acc_name>* veri paylaşma kaynağınızın adı olduğunu unutmayın. Henüz bir veri paylaşma kaynağı oluşturmadıysanız, bu ön koşul daha sonra geri dönebilirsiniz.  

* SYNAPSE çalışma alanı güvenlik duvarı erişimi. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal ' de SYNAPSE çalışma alanı ' na gidin. Sol gezinmede *güvenlik duvarları* ' nı seçin.
    1. *Azure hizmetlerinin ve kaynaklarının bu çalışma alanına erişmesine Izin vermek* için **Açık** ' a tıklayın.
    1. **+ İstemci IP 'Si Ekle**' ye tıklayın. İstemci IP adresi değişebilir. Bu işlemin bir sonraki Azure portal SQL verilerini paylaşışınızda tekrarlanması gerekebilir. Ayrıca, bir IP aralığı ekleyebilirsiniz.
    1. **Kaydet**’e tıklayın. 


### <a name="share-from-azure-data-explorer"></a>Azure Veri Gezgini'nden paylaşma
* Paylaşmak istediğiniz veritabanlarına sahip bir Azure Veri Gezgini kümesi.
* *Microsoft. kusto/kümeler/Write* Içinde bulunan Azure Veri Gezgini kümesine yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-data-share-account"></a>Veri paylaşma hesabı oluşturma

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure Kaynak grubunda bir Azure veri paylaşma kaynağı oluşturun.

1. Portalın sol üst köşesindeki menü düğmesini seçin ve ardından **kaynak oluştur** (+) seçeneğini belirleyin.

1. *Veri paylaşımında* arama yapın.

1. Veri paylaşma ' yı seçin ve **Oluştur**' u seçin.

1. Aşağıdaki bilgilerle Azure veri paylaşma kaynağınızın temel ayrıntılarını doldurun. 

     **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Abonelik | Aboneliğiniz | Veri paylaşma hesabınız için kullanmak istediğiniz Azure aboneliğini seçin.|
    | Kaynak grubu | *testresourcegroup* | Mevcut bir kaynak grubunu kullanın veya yeni bir kaynak grubu oluşturun. |
    | Konum | *Doğu ABD 2* | Veri paylaşma hesabınız için bir bölge seçin.
    | Name | *datashareaccount* | Veri paylaşma hesabınız için bir ad belirtin. |
    | | |

1. Veri paylaşma hesabınızı sağlamak için **gözden geçir + oluştur**' u seçin ve **Oluştur** ' a tıklayın. Yeni bir veri paylaşma hesabının sağlanması genellikle yaklaşık 2 dakika veya daha kısa sürer. 

1. Dağıtım tamamlandığında **Kaynağa Git**' i seçin.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Kaynak grubunda bir Azure veri paylaşma kaynağı oluşturun.

Başlangıç olarak ortamınızı Azure CLI için hazırlayın:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Kaynağı oluşturmak için şu komutları kullanın:

1. Aboneliğinizi geçerli varsayılan abonelik olacak şekilde ayarlamak için [az Account set](/cli/azure/account#az_account_set) komutunu kullanın:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Kaynak sağlayıcısını kaydetmek için [az Provider Register](/cli/azure/provider#az_provider_register) komutunu çalıştırın:

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. [az group create](/cli/azure/group#az_group_create) komutunu çalıştırarak bir kaynak grubu oluşturun veya mevcut bir kaynak grubunu kullanın:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Veri paylaşma hesabı oluşturmak için [az datashare Account Create](/cli/azure/datashare/account#az_datashare_account_create) komutunu çalıştırın:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Veri paylaşma hesaplarınızı görmek için [az datashare hesap listesi](/cli/azure/datashare/account#az_datashare_account_list) komutunu çalıştırın:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Bir paylaşma oluşturun

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Veri paylaşımında genel bakış sayfasına gidin.

    ![Verilerinizi paylaşma](./media/share-receive-data.png "Verilerinizi paylaşma") 

1. **Verilerinizi paylaşmayı Başlat**' ı seçin.

1. **Oluştur**’u seçin.   

1. Paylaşımınızın ayrıntılarını doldurun. Bir ad, paylaşma türü, içerik paylaşma açıklaması ve kullanım koşulları (isteğe bağlı) belirtin. 

    ![EnterShareDetails](./media/enter-share-details.png "Paylaşma ayrıntılarını girin") 

1. **Devam**’ı seçin.

1. Paylaşımınıza veri kümeleri eklemek için **veri kümesi Ekle**' yi seçin. 

    ![Paylaşımınıza veri kümeleri ekleme](./media/datasets.png "Veri kümeleri")

1. Eklemek istediğiniz veri kümesi türünü seçin. Önceki adımda seçtiğiniz paylaşma türüne (anlık görüntü veya yerinde) bağlı olarak farklı bir veri kümesi türleri listesi görürsünüz. Bir Azure SQL veritabanından veya Azure SYNAPSE Analytics 'ten (eskiden Azure SQL DW) paylaşıyorsanız, tabloları listelemek için kimlik doğrulama yöntemi istenir. AAD kimlik doğrulaması ' nı seçin ve **veri paylaşımının "Kullanıcı oluşturma" komut dosyasını benim yerime çalıştırmasına Izin ver** onay kutusunu işaretleyin. 

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

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Veri paylaşma oluşturmak için [az Storage Account Create](/cli/azure/storage/account#az_storage_account_create) komutunu çalıştırın:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Önceki komutta Share için bir kapsayıcı oluşturmak için [az Storage Container Create](/cli/azure/storage/container#az_storage_container_create) komutunu kullanın:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Veri paylaşımınızı oluşturmak için [az datashare Create](/cli/azure/datashare#az_datashare_create) komutunu çalıştırın:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Belirtilen adrese yönelik daveti oluşturmak için [az datashare davet Create](/cli/azure/datashare/invitation#az_datashare_invitation_create) komutunu kullanın:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

Azure veri paylaşımınız artık oluşturulmuştur ve veri paylaşımınızın alıcısı artık davetinizi kabul etmeye hazırdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak artık gerekli olmadığında, **veri paylaşımının genel bakış** sayfasına gidin ve **Sil** ' i seçerek kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure veri paylaşımının nasıl oluşturulacağını ve alıcıların nasıl davet alınacağını öğrenirsiniz. Bir veri tüketicisinin bir veri paylaşımının kabul edip alabileceği hakkında bilgi edinmek için, verileri kabul etme ve alma öğreticisine geçin.

> [!div class="nextstepaction"]
> [Öğretici: Azure Veri Paylaşımı’nı kullanarak veri kabul etme ve alma](subscribe-to-data-share.md)
