---
title: 'Öğretici: & alma verileri kabul etme-Azure veri paylaşma'
description: Öğretici-Azure veri paylaşımının kullanıldığı verileri kabul edin ve alın
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 9c24f54fe846459187488b0a65b2582914e25e2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499345"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Öğretici: Azure veri paylaşımının kullanıldığı verileri kabul edin ve alın  

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
* Veri paylaşma daveti: "Azure Data Share davetiyesi **<yourdataprovider@domain.com>** " başlıklı konuyla bir Microsoft Azure daveti.

### <a name="receive-data-into-a-storage-account"></a>Bir depolama hesabına veri alma: 

* Azure depolama hesabı: henüz yoksa bir [Azure depolama hesabı](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)oluşturabilirsiniz. 
* *Microsoft. Authorization/role atamalar/Write* izninde bulunan depolama hesabına rol ataması ekleme izni. Bu izin sahip rolünde bulunur. 
* Microsoft. DataShare için kaynak sağlayıcısı kaydı. Bunun nasıl yapılacağı hakkında bilgi için bkz. [Azure kaynak sağlayıcıları](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) belgeleri. 

> [!IMPORTANT]
> Bir Azure veri paylaşımının kabul edileceği ve alınacağı, önce Microsoft. DataShare kaynak sağlayıcısını kaydetmeniz ve verileri kabul ettiğiniz depolama hesabının sahibi olmanız gerekir. Veri paylaşımının kaynak sağlayıcısını kaydetmek için [Azure veri paylaşımında sorun giderme](data-share-troubleshoot.md) ve depolama hesabının sahibi olarak kendiniz ekleme konularında belgelenen yönergeleri izleyin. 

### <a name="receive-data-into-a-sql-based-source"></a>SQL tabanlı bir kaynağa veri alma:

* Azure SQL veritabanı veya Azure SQL veri ambarı 'na erişmek için veri paylaşımının MSI izni. Bu, aşağıdaki adımlarla yapılabilir: 
    1. Kendi kendinize sunucu için Azure Active Directory yöneticisi olarak ayarlayın.
    1. Azure Active Directory kullanarak Azure SQL veritabanı/veri ambarına bağlanın.
    1. Veri paylaşımının MSI 'sini db_owner olarak eklemek için aşağıdaki betiği yürütmek üzere sorgu Düzenleyicisi 'ni (Önizleme) kullanın. SQL Server kimlik doğrulaması değil Active Directory kullanarak bağlanmanız gerekir. 

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
*< Share_acc_name >* , veri paylaşma hesabınızın adı olduğunu unutmayın. Henüz bir veri paylaşma hesabı oluşturmadıysanız, bu ön koşul daha sonra geri dönebilirsiniz.         

* İstemci IP SQL Server güvenlik duvarı erişimi: Bu, şu adımlarla yapılabilir: 1. *Güvenlik duvarları ve sanal ağlar 1 '* e gidin. Azure hizmetlerine erişime izin vermek **için Aç düğmesine** tıklayın. 

Bu önkoşulları tamamladıktan sonra, SQL Server veri almaya hazırsınızdır.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="open-invitation"></a>Daveti aç

Veri sağlayıcınızdan bir davet için gelen kutunuzu denetleyin. Davet, **<yourdataprovider@domain.com>Azure Veri Paylaşma davetini** başlıklı Microsoft Azure. Birden çok davetiye varsa doğru paylaşımın kabul edildiğinizden emin olmak için, paylaşılan adı ' nı bir yere göz atın. 

Davetinizi Azure 'da görmek için **daveti görüntüle** ' yi seçin. Bu sizi aldığınız paylaşımlar görünümüne götürür.

![Lerinin](./media/invitations.png "Davetlerin listesi") 

Görüntülemek istediğiniz paylaşma seçeneğini belirleyin. 

## <a name="accept-invitation"></a>Daveti kabul et
**Kullanım koşulları**dahil olmak üzere tüm alanların incelendiğini doğrulayın. Kullanım koşullarını kabul ediyorsanız, kabul ettiğinizi belirtmek için kutuyu denetlemeniz gerekir. 

![Kullanım koşulları](./media/terms-of-use.png "Kullanım koşulları") 

*Hedef veri paylaşma hesabı*altında, veri paylaşımınızı dağıtmak istediğiniz aboneliği ve kaynak grubunu seçin. 

**Veri** paylaşma hesabı alanı için, mevcut bir veri paylaşma hesabınız yoksa **Yeni oluştur** ' u seçin. Aksi takdirde, veri paylaşımınızı kabul etmek istediğiniz mevcut bir veri paylaşma hesabı seçin. 

*Alınan paylaşma adı* alanı Için, veriler tarafından belirtilen varsayılanı bırakabilir veya alınan paylaşıma yönelik yeni bir ad belirtebilirsiniz. 

![Hedef veri paylaşma hesabı](./media/target-data-share.png "Hedef veri paylaşma hesabı") 

Kullanım koşullarını kabul ettikten ve paylaşımınız için bir konum belirttikten sonra *kabul et ve Yapılandır*' ı seçin. Bu seçeneği belirlerseniz, bir paylaşılan abonelik oluşturulur ve bir sonraki ekranda, verilerinize kopyalanması için bir hedef depolama hesabı seçmeniz istenir. 

![Kabul etme seçenekleri](./media/accept-options.png "Kabul etme seçenekleri") 

Şimdi daveti kabul etmek, ancak depolama alanınızı daha sonra yapılandırmak isterseniz, *kabul et ' i ve daha sonra Yapılandır*' ı seçin. Bu seçenek, hedef depolama hesabınızı daha sonra yapılandırmanıza olanak tanır. Depolama alanınızı daha sonra yapılandırmaya devam etmek için, bkz. [depolama hesabınızı yapılandırma](how-to-configure-mapping.md) sayfası, veri paylaşma yapılandırmanızın nasıl sürdürüleceği hakkında ayrıntılı adımlar için. 

Daveti kabul etmek istemiyorsanız *Reddet*' i seçin. 

## <a name="configure-storage"></a>Depolama alanını yapılandırma
*Hedef depolama ayarları*altında, verilerinizi almak istediğiniz aboneliği, kaynak grubunu ve depolama hesabını seçin. 

![Hedef depolama ayarları](./media/target-storage-settings.png "Hedef depolama") 

Verilerinizin düzenli olarak yenilenmesini almak için, anlık görüntü ayarlarını etkinleştirdiğinizden emin olun. Veri sağlayıcınız onu veri paylaşımında içerdiğine yalnızca bir anlık görüntü ayarı görürsünüz. 

![Anlık görüntü ayarları](./media/snapshot-settings.png "Anlık görüntü ayarları") 

*Kaydet*’i seçin. 

> [!IMPORTANT]
> SQL tabanlı veriler alıyorsanız ve bu verileri SQL tabanlı bir kaynağa almak istiyorsanız, veri kümeniz için hedef olarak bir SQL Server yapılandırma hakkında bilgi edinmek için [bir veri kümesi eşleme](how-to-configure-mapping.md) nasıl yapılır Kılavuzu ' nu ziyaret edin. 

## <a name="trigger-a-snapshot"></a>Anlık görüntü tetikleyin

Alınan paylaşımlar-> Ayrıntılar sekmesinde bir anlık görüntüyü **tetikleyici anlık görüntüsünü**seçerek tetikleyebilirsiniz. Burada, verilerinizin tam veya artımlı anlık görüntüsünü tetikleyebilirsiniz. Veri sağlayıcınızdan ilk kez veri alıyorsanız tam kopya ' ı seçin. 

![Görüntüyü Tetikle](./media/trigger-snapshot.png "Görüntüyü Tetikle") 

Son çalıştırma durumu *başarılı*olduğunda, alınan verileri görüntülemek için depolama hesabını açın. 

Kullandığınız depolama hesabını denetlemek için **veri kümeleri**' ni seçin. 

![Tüketici veri kümeleri](./media/consumer-datasets.png "Tüketici veri kümesi eşleme") 

## <a name="view-history"></a>Geçmişi görüntüleme
Anlık görüntülerinizin geçmişini görüntülemek için, alınan paylaşımlar-> geçmişi ' ne gidin. Burada, son 60 gün için oluşturulan tüm anlık görüntülerin geçmişini bulacaksınız. 

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, bir Azure veri paylaşımının nasıl kabul edileceği ve alınacağı ile karşılaşacağınızı öğrenirsiniz. Azure veri paylaşımında kavramlar hakkında daha fazla bilgi edinmek için bkz. [Azure veri paylaşma terminolojisi](terminology.md).