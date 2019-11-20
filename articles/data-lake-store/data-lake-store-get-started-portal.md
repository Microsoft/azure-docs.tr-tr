---
title: Azure Data Lake Storage 1.-Portal ile çalışmaya başlama
description: Bir Data Lake Storage 1. hesabı oluşturmak ve hesapta temel işlemleri gerçekleştirmek için Azure portal kullanın.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: e5a3df82fae9d81a870d3dba929bc7e4cd4123d2
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904688"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Azure portal kullanarak Azure Data Lake Storage 1. kullanmaya başlama

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Bir Data Lake Storage 1. hesabı oluşturmak ve klasör oluşturma, veri dosyalarını karşıya yükleme ve indirme, hesabınızı silme gibi temel işlemleri gerçekleştirmek için Azure portal nasıl kullanacağınızı öğrenin. Daha fazla bilgi için bkz. [Azure Data Lake Storage 1. genel bakış](data-lake-store-overview.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce aşağıdaki öğelere sahip olmanız gerekir:

* **Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage 1. hesabı oluşturma

1. Yeni [Azure Portal](https://portal.azure.com)'da oturum açın.
2. **Kaynak oluştur > depolama > Data Lake Storage 1.** ' ya tıklayın.
3. **Yeni Data Lake Storage 1.** dikey penceresinde, aşağıdaki ekran görüntüsünde gösterilen değerleri sağlayın:

    ![Yeni bir Data Lake Storage 1. hesabı oluşturun](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Yeni bir Data Lake Storage 1. hesabı oluşturun")

   * **Ad**. Data Lake Storage 1. hesabı için benzersiz bir ad girin.
   * **Abonelik**. Yeni Data Lake Storage 1. hesabını oluşturmak istediğiniz aboneliği seçin.
   * **Kaynak Grubu**. Mevcut bir kaynak grubunu seçin veya yeni grup oluşturmak için **Yeni oluştur** seçeneğini belirleyin. Kaynak grubu, bir uygulama için ilgili kaynakları bir arada tutan bir kapsayıcıdır. Daha fazla bilgi için bkz. [Azure'da Kaynak Grupları](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Konum**: Data Lake Storage 1. hesabını oluşturmak istediğiniz konumu seçin.
   * **Şifreleme Ayarları**. Üç seçenek vardır:

     * **Şifrelemeyi etkinleştirme**.
     * Şifreleme anahtarlarınızı Data Lake Storage 1. yönetmek istiyorsanız **Data Lake Storage 1. tarafından yönetilen anahtarları kullanın**.
     * **Kendi Anahtar Kasanızdaki anahtarları kullanın**. Mevcut bir Azure Key Vault’u seçin veya yeni bir Key Vault oluşturun. Anahtarları bir Key Vault kullanmak için, Azure Key Vault erişmek üzere Data Lake Storage 1. hesabı için izinler atamanız gerekir. Yönergeler için bkz. [Azure Key Vault'a izinleri atama](#assign-permissions-to-azure-key-vault).

        ![Data Lake Storage 1. şifreleme](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Storage 1. şifreleme")

        **Şifreleme Ayarları** dikey penceresinde **Tamam**’a tıklayın.

        Daha fazla bilgi için bkz. [Azure Data Lake Storage 1. verileri şifreleme](./data-lake-store-encryption.md).

4. **Oluştur**'a tıklayın. Hesabı panoya sabitlemeyi seçtiyseniz, panoya geri götürülürsünüz ve Data Lake Storage 1. hesabı hazırlamanın ilerlemesini görebilirsiniz. Data Lake Storage 1. hesap sağlandıktan sonra hesap dikey penceresi görüntülenir.

## <a name="assign-permissions-to-azure-key-vault"></a>Azure Key Vault’a izin atama

Data Lake Storage 1. hesabında şifrelemeyi yapılandırmak için bir Azure Key Vault anahtarlar kullandıysanız Data Lake Storage 1. hesabı ve Azure Key Vault hesabı arasında erişimi yapılandırmanız gerekir. Bunu yapmak için aşağıdaki adımları gerçekleştirin.

1. Azure Key Vault anahtarları kullandıysanız, Data Lake Storage 1. hesabının dikey penceresi üst kısımdaki bir uyarı görüntüler. Uyarıya tıklayarak **Şifreleme** sayfasını açın.

    ![Data Lake Storage 1. şifreleme](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Storage 1. şifreleme")
2. Bu dikey pencere, erişimi yapılandırmak için iki seçenek gösterir.

    ![Data Lake Storage 1. şifreleme](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Data Lake Storage 1. şifreleme")

   * Erişim yapılandırmak için ilk seçenekte **İzin Ver**'e tıklayın. İlk seçenek yalnızca Data Lake Storage 1. hesabını oluşturan kullanıcı da Azure Key Vault Yöneticisi olduğunda etkindir.
   * Diğer seçenek, dikey pencerede görüntülenen PowerShell cmdlet’ini çalıştırmak içindir. Azure Anahtar Kasası’nın sahibi olmanız ya da Azure Anahtar Kasası’nda izin verme yetkisine sahip olmanız gerekir. Cmdlet’i çalıştırdıktan sonra, erişimi yapılandırmak için dikey pencereye dönün ve **Etkinleştir**’e tıklayın.

> [!NOTE]
> Ayrıca, Azure Resource Manager şablonları kullanarak Data Lake Storage 1. bir hesap oluşturabilirsiniz. Bu şablonlara [Azure Hızlı Başlangıç Şablonları](https://azure.microsoft.com/resources/templates/?term=data+lake+store)’ndan erişebilirsiniz:
> * Veri şifrelemesi olmadan: [Azure Data Lake Storage 1. hesabını veri şifrelemesi olmadan dağıtın](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> * Data Lake Storage 1. kullanarak veri şifrelemesi ile: [şifreleme ile Data Lake Storage 1. hesabı dağıtın (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * Azure Key Vault kullanarak veri şifrelemesi ile: [şifreleme ile Data Lake Storage 1. hesabı dağıtın (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="createfolder"></a>Klasör oluşturma

Data Lake Storage 1. hesabınızda, verileri yönetmek ve depolamak için klasörler oluşturabilirsiniz.

1. Oluşturduğunuz Data Lake Storage 1. hesabını açın. Sol bölmede **tüm kaynaklar**' a tıklayın ve ardından **tüm kaynaklar** dikey penceresinde, klasör oluşturmak istediğiniz hesap adına tıklayın. Hesabı başlangıç panosuna sabitlediyseniz bu hesap kutucuğuna tıklayın.
2. Data Lake Storage 1. hesabı dikey penceresinde **Veri Gezgini**' e tıklayın.

    ![Data Lake Storage 1. hesapta klasör oluşturma](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Data Lake Storage 1. hesapta klasör oluşturma")
3. Veri Gezgini dikey penceresinde, **Yeni Klasör**'e tıklayın, yeni klasör için bir ad girin ve ardından **Tamam**'a tıklayın.

    ![Data Lake Storage 1. hesapta klasör oluşturma](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Data Lake Storage 1. hesapta klasör oluşturma")

    Yeni oluşturulan klasör, **Veri Gezgini** dikey penceresinde listelenir. İç içe geçmiş klasörleri istediğiniz düzeye kadar oluşturabilirsiniz.

    ![Data Lake hesapta klasör oluşturma](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Data Lake hesapta klasör oluşturma")

## <a name="uploaddata"></a>Karşıya veri yükleme

Verilerinizi bir Data Lake Storage 1. hesabına doğrudan kök düzeyinde veya hesap içinde oluşturduğunuz bir klasöre yükleyebilirsiniz.

1. **Veri Gezgini** dikey penceresinde **Yükle**'ye tıklayın.
2. **Dosyaları karşıya yükleme** dikey penceresinde yüklemek istediğiniz dosyaları bulun ve **Seçili dosyaları ekle**'ye tıklayın. Yüklemek için birden fazla dosya da seçebilirsiniz.

    ![Karşıya veri yükleme](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Karşıya veri yükleme")

Karşıya yüklenecek örnek veri arıyorsanız **Azure Data Lake Git Deposu**'ndan [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) klasörünü alabilirsiniz.

## <a name="properties"></a>Depolanan verilerde kullanılabilen eylemler

Dosyanın yanındaki üç nokta simgesine tıklayın ve açılan menüden verilerle ilgili gerçekleştirmek istediğiniz eyleme tıklayın.

![Verilerdeki Özellikler](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Verilere yönelik özellikler")

## <a name="secure-your-data"></a>Verilerinizin güvenliğini sağlama

Azure Active Directory ve erişim denetimi (ACL 'Ler) kullanarak Data Lake Storage 1. hesabınızda depolanan verileri güvenli hale getirebilirsiniz. Bunun nasıl yapılacağı hakkında yönergeler için bkz. [Azure Data Lake Storage 1. verileri güvenli hale getirme](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Hesabınızı silme

Bir Data Lake Storage 1. hesabını silmek için Data Lake Storage 1. dikey penceresinden **Sil**' e tıklayın. Eylemi onaylamak için silmek istediğiniz hesabın adını girmeniz istenir. Hesabın adını girin ve ardından **Sil**'e tıklayın.

![Data Lake Storage 1. hesabı Sil](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Data Lake hesabını silme")

## <a name="next-steps"></a>Sonraki adımlar

* [Büyük veri gereksinimleri için Azure Data Lake Storage 1. kullanma](data-lake-store-data-scenarios.md)
* [Data Lake Storage Gen1'de verilerin güvenliğini sağlama](data-lake-store-secure-data.md)
* [Data Lake Storage 1. ile Azure Data Lake Analytics kullanma](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight 'ı Data Lake Storage 1. ile kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)
