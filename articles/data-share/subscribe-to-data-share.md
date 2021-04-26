---
title: 'Öğretici: & alma verileri kabul etme-Azure veri paylaşma'
description: Öğretici-Azure veri paylaşımının kullanıldığı verileri kabul edin ve alın
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: d063a0870616b5b977df18c56d9d66515b03d0a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870869"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Öğretici: Azure Veri Paylaşımı’nı kullanarak veri kabul etme ve alma  

Bu öğreticide, Azure veri paylaşma kullanarak bir veri paylaşımının davetini kabul etme hakkında bilgi edineceksiniz. Sizinle paylaşılan verilerin nasıl alınacağını ve her zaman sizinle paylaşılan verilerin en son anlık görüntüsüne sahip olduğunuzdan emin olmak için düzenli yenileme aralığını nasıl etkinleştireceğinizi öğreneceksiniz. 

> [!div class="checklist"]
> * Azure Veri Paylaşma davetini kabul etme
> * Azure veri paylaşma hesabı oluşturma
> * Verileriniz için bir hedef belirtin
> * Zamanlanmış yenileme için veri paylaşımınıza abonelik oluşturma

## <a name="prerequisites"></a>Önkoşullar
Bir veri paylaşma davetini kabul etmeden önce, aşağıda listelenen birkaç Azure kaynağı sağlamalısınız. 

Bir veri paylaşma davetini kabul etmeden önce tüm önkoşulların tümünün tamamlandığından emin olun. 

* Azure aboneliği: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Veri paylaşma daveti: "Azure Data Share davetiyesi" başlıklı konuyla bir Microsoft Azure daveti **<yourdataprovider@domain.com>** .
* [Microsoft. DataShare kaynak sağlayıcısını](concepts-roles-permissions.md#resource-provider-registration) , bir veri paylaşma kaynağı ve hedef Azure veri mağazalarınızın bulunduğu Azure aboneliğini oluşturacağınız Azure aboneliğine kaydedin.

### <a name="receive-data-into-a-storage-account"></a>Bir depolama hesabına veri alma

* Azure depolama hesabı: henüz yoksa bir [Azure depolama hesabı](../storage/common/storage-account-create.md)oluşturabilirsiniz. 
* *Microsoft. Storage/storageAccounts/Write* içinde bulunan depolama hesabına yazma izni. Bu izin Katkıda Bulunan rolünde vardır. 
* *Microsoft. Authorization/role atamalar/Write*' de bulunan depolama hesabına rol ataması ekleme izni. Bu izin Sahip rolünde vardır.  

### <a name="receive-data-into-a-sql-based-target"></a>SQL tabanlı bir hedefte veri alma
Azure SQL veritabanı 'na veri almayı seçerseniz Azure SYNAPSE Analytics, önkoşul listesidir. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Azure SQL veritabanı veya Azure SYNAPSE Analytics 'e veri alma önkoşulları (eski adıyla Azure SQL DW)

* Azure SQL veritabanı veya Azure SYNAPSE Analytics (eski adıyla Azure SQL DW).
* *Microsoft. SQL/Servers/veritabanları/Write*'TA bulunan SQL Server 'da veritabanlarına yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır. 
* SQL Server **Azure Active Directory Yöneticisi**
* Güvenlik duvarı erişimi SQL Server. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal 'deki SQL Server 'da *güvenlik duvarları ve sanal ağlar* ' a gidin.
    1. *Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin vermek* için **Evet** ' i tıklatın.
    1. **+ İstemci IP 'Si Ekle**' ye tıklayın. İstemci IP adresi değişebilir. Bu işlemin bir sonraki Azure portal SQL verilerini paylaşışınızda tekrarlanması gerekebilir. Ayrıca, bir IP aralığı ekleyebilirsiniz.
    1. **Kaydet**’e tıklayın. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Azure SYNAPSE Analytics (çalışma alanı) SQL havuzu 'na veri alma önkoşulları

* Bir Azure SYNAPSE Analytics (çalışma alanı) adanmış SQL Havuzu. Sunucusuz SQL havuzunda verilerin alınması Şu anda desteklenmiyor.
* *Microsoft. SYNAPSE/Workspaces/Sqlhavuzlar/Write* Içinde bulunan SYNAPSE çalışma alanındaki SQL havuzuna yazma izni. Bu izin **Katkıda Bulunan** rolünde vardır.
* SYNAPSE çalışma alanı SQL havuzuna erişmek için veri paylaşımının yönetilen kimliği izni. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal ' de SYNAPSE çalışma alanı ' na gidin. Sol gezinmede SQL Active Directory Yöneticisi ' ni seçin ve kendiniz **Azure Active Directory Yöneticisi** olarak ayarlayın.
    1. SYNAPSE Studio 'yu açın, sol gezinmede *Yönet* ' i seçin. Güvenlik altında *erişim denetimi* ' ni seçin. Kendinize **SQL Yöneticisi** veya **çalışma alanı yönetici** rolü atayın.
    1. SYNAPSE Studio 'da sol gezinmeden *Geliştir* ' i seçin. Veri paylaşımının kaynak yönetimli kimliğini bir ' db_datareader, db_datawriter, db_ddladmin ' olarak eklemek için SQL havuzunda aşağıdaki betiği yürütün. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       *<share_acc_name>* veri paylaşma kaynağınızın adı olduğunu unutmayın. Henüz bir veri paylaşma kaynağı oluşturmadıysanız, bu ön koşul daha sonra geri dönebilirsiniz.  

* SYNAPSE çalışma alanı güvenlik duvarı erişimi. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Azure portal ' de SYNAPSE çalışma alanı ' na gidin. Sol gezinmede *güvenlik duvarları* ' nı seçin.
    1. *Azure hizmetlerinin ve kaynaklarının bu çalışma alanına erişmesine Izin vermek* için **Açık** ' a tıklayın.
    1. **+ İstemci IP 'Si Ekle**' ye tıklayın. İstemci IP adresi değişebilir. Bu işlemin bir sonraki Azure portal SQL verilerini paylaşışınızda tekrarlanması gerekebilir. Ayrıca, bir IP aralığı ekleyebilirsiniz.
    1. **Kaydet**’e tıklayın. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Azure Veri Gezgini kümesine veri alma: 

* Veri sağlayıcısının Veri Gezgini kümesiyle aynı Azure veri merkezindeki bir Azure Veri Gezgini kümesi: henüz yoksa bir [azure Veri Gezgini kümesi](/azure/data-explorer/create-cluster-database-portal)oluşturabilirsiniz. Veri sağlayıcısının kümesinin Azure veri merkezini bilmiyorsanız, kümeyi daha sonra işlem içinde oluşturabilirsiniz.
* *Microsoft. kusto/kümeler/Write* Içinde bulunan Azure Veri Gezgini kümesine yazma izni. Bu izin Katkıda Bulunan rolünde vardır. 

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="open-invitation"></a>Daveti aç

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Daveti e-postayla veya doğrudan Azure portal açabilirsiniz. 

   E-postadaki daveti açmak için, veri sağlayıcınızdan bir davet için gelen kutunuza bakın. Davet, ' **den <yourdataprovider@domain.com> Azure veri paylaşma daveti** başlıklı Microsoft Azure. Azure 'da davetinizi görmek için **daveti görüntüle** ' ye tıklayın. 

   Daveti doğrudan Azure portal açmak için Azure portal **veri paylaşma davetlerini** arayın. Bu eylem sizi veri paylaşma davetlerinin listesine götürür.

   ![Davetlerin listesi](./media/invitations.png "Davetlerin listesi") 

1. Görüntülemek istediğiniz paylaşma seçeneğini belirleyin. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ortamınızı hazırlayın ve ardından davetlerinizi görüntüleyin.

Başlangıç olarak ortamınızı Azure CLI için hazırlayın:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Geçerli davetlerinizi görmek için [az datashare Consumer davet List](/cli/azure/datashare/consumer/invitation#az_datashare_consumer_invitation_list) komutunu çalıştırın:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Bir sonraki bölümde kullanım için davet KIMLIĞINIZI kopyalayın.

---

## <a name="accept-invitation"></a>Daveti kabul et

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. **Kullanım koşulları** dahil olmak üzere tüm alanların incelendiğini doğrulayın. Kullanım koşullarını kabul ediyorsanız, kabul ettiğinizi belirtmek için kutuyu denetlemeniz gerekir. 

   ![Kullanım koşulları](./media/terms-of-use.png "Kullanım koşulları") 

1. *Hedef veri paylaşma hesabı* altında, veri paylaşımınızı dağıtmak istediğiniz aboneliği ve kaynak grubunu seçin. 

   **Veri** paylaşma hesabı alanı için, mevcut bir veri paylaşma hesabınız yoksa **Yeni oluştur** ' u seçin. Aksi takdirde, veri paylaşımınızı kabul etmek istediğiniz mevcut bir veri paylaşma hesabı seçin. 

   **Alınan paylaşma adı** alanı için, veriler tarafından belirtilen varsayılanı bırakabilir veya alınan paylaşıma yönelik yeni bir ad belirtebilirsiniz. 

   Kullanım koşullarını kabul ettikten ve alınan paylaşımınızı yönetmek için bir veri paylaşma hesabı belirttikten sonra **kabul et ve Yapılandır**' ı seçin. Bir paylaşma aboneliği oluşturulacak. 

   ![Kabul etme seçenekleri](./media/accept-options.png "Kabul etme seçenekleri") 

   Bu eylem sizi veri paylaşma hesabınızdaki alınan paylaşıma götürür. 

   Daveti kabul etmek istemiyorsanız *Reddet*' i seçin. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Veri paylaşımının oluşturulması için [az datashare Consumer Share-Subscription Create](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_create) komutunu kullanın.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>Alınan paylaşma yapılandırma

### <a name="portal"></a>[Portal](#tab/azure-portal)

Verileri almak istediğiniz yeri yapılandırmak için aşağıdaki adımları izleyin.

1. **Veri kümeleri** sekmesini seçin. Hedef atamak istediğiniz veri kümesinin yanındaki kutuyu işaretleyin. Hedef veri deposu seçmek için **+ hedefe eşle** öğesini seçin. 

   ![Hedefle eşle](./media/dataset-map-target.png "Hedefle eşle") 

1. Verilerin içinde olmasını istediğiniz hedef veri deposu türünü seçin. Hedef veri deposundaki tüm veri dosyaları veya tablolar aynı yol ve ada sahip olacak şekilde üzerine yazılır. Azure SQL veritabanı veya Azure SYNAPSE Analytics (eski adıyla Azure SQL DW) için veri alıyorsanız, **veri paylaşımının "Kullanıcı oluşturma" komut dosyasını benim yerime çalıştırmasına Izin ver** onay kutusunu işaretleyin.

   Yerinde paylaşım için, belirtilen konumda bir veri deposu seçin. Konum, veri sağlayıcısının kaynak veri deposunun bulunduğu Azure veri merkezindedir. Veri kümesi eşlendikten sonra, verilere erişmek için Hedef yoldaki bağlantıyı takip edebilirsiniz.

   ![Hedef depolama hesabı](./media/dataset-map-target-sql.png "Hedef depolama") 

1. Anlık görüntü tabanlı paylaşım için, veri sağlayıcısı verilere düzenli güncelleştirme sağlamak üzere bir anlık görüntü zamanlaması oluşturmışsa, anlık görüntü **zamanlama** sekmesini seçerek de anlık görüntü zamanlamasını etkinleştirebilirsiniz. Anlık görüntü zamanlamasının yanındaki kutuyu işaretleyin ve **+ Etkinleştir**' i seçin. Zamanlanan ilk anlık görüntünün, zamanlama zamanının bir dakikası içinde ve sonraki anlık görüntülerin zamanlanan sürenin saniye içinde başlatılacağını unutmayın.

   ![Anlık görüntü zamanlamasını etkinleştir](./media/enable-snapshot-schedule.png "Anlık görüntü zamanlamasını etkinleştir")

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Veri almak istediğiniz yeri yapılandırmak için bu komutları kullanın.

1. Veri kümesi KIMLIĞINI almak için [az datashare tüketicisi Share-Subscription List-Source-DataSet](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_dataset) komutunu çalıştırın:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Bu veri paylaşımında bir depolama hesabı oluşturmak için [az Storage Account Create](/cli/azure/storage/account#az_storage_account_create) komutunu çalıştırın:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Depolama hesabı KIMLIĞINI almak için [az Storage Account Show](/cli/azure/storage/account#az_storage_account_show) komutunu kullanın:

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. Hesap asıl KIMLIĞINI almak için aşağıdaki komutu kullanın:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Hesap sorumlusu için bir rol ataması oluşturmak için [az role atama Create](/cli/azure/role/assignment#az_role_assignment_create) komutunu kullanın:

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Veri kümesi KIMLIĞINI temel alan eşleme için bir değişken oluşturun:

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. Veri kümesi eşlemesini oluşturmak için [az datashare tüketicisi DataSet-Mapping Create](/cli/azure/datashare/consumer/dataset-mapping#az_datashare_consumer_dataset_mapping_create) komutunu kullanın:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Veri kümesi eşitlemesini başlatmak için [az datashare Consumer Share-Subscription Synchronization start](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_start) komutunu çalıştırın.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Eşitlerinizin bir listesini görmek için [az datashare Consumer Share-Subscription Synchronization List](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_list) komutunu çalıştırın:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Paylaşımınızda ayarlanan eşitleme ayarlarını görmek için [az datashare Consumer Share-Subscription List-Source-Share-Synchronization-Setting](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) komutunu kullanın.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Anlık görüntü tetikleyin

### <a name="portal"></a>[Portal](#tab/azure-portal)

Bu adımlar yalnızca anlık görüntü tabanlı paylaşım için geçerlidir.

1. **Ayrıntılar** sekmesini ve ardından **tetikleyici anlık görüntüsünü** seçerek bir anlık görüntü tetikleyebilirsiniz. Burada, verilerinizin tam veya artımlı anlık görüntüsünü tetikleyebilirsiniz. Veri sağlayıcınızdan ilk kez veri alıyorsanız tam kopya ' ı seçin. 

   ![Görüntüyü Tetikle](./media/trigger-snapshot.png "Görüntüyü Tetikle") 

1. Son çalıştırma durumu *başarılı* olduğunda, alınan verileri görüntülemek için hedef veri deposuna gidin. **Veri kümeleri**' ni seçin ve Hedef yoldaki bağlantıya tıklayın. 

   ![Tüketici veri kümeleri](./media/consumer-datasets.png "Tüketici veri kümesi eşleme") 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bir anlık görüntü tetiklemek için [az datashare tüketici tetikleyicisi Create](/cli/azure/datashare/consumer/trigger#az_datashare_consumer_trigger_create) komutunu çalıştırın:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Bu komutu yalnızca anlık görüntü tabanlı paylaşım için kullanın.

---

## <a name="view-history"></a>Geçmişi görüntüleme
Bu adım yalnızca anlık görüntü tabanlı paylaşım için geçerlidir. Anlık görüntülerinizin geçmişini görüntülemek için **Geçmiş** sekmesini seçin. Burada, son 30 gün için oluşturulan tüm anlık görüntülerin geçmişini bulacaksınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak artık gerekli olmadığında, **veri paylaşımının genel bakış** sayfasına gidin ve **Sil** ' i seçerek kaldırın.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, bir Azure veri paylaşımının nasıl kabul edileceği ve alınacağı hakkında daha fazla öğrendiniz. Azure veri paylaşımıyla ilgili kavramlar hakkında daha fazla bilgi edinmek için Azure veri paylaşma terminolojisi ' ne geçin.

> [!div class="nextstepaction"]
> [Azure veri paylaşımında kavramlar](terminology.md)