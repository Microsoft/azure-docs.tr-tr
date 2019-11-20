---
title: Azure Tablo Depolaması | Azure Marketi
description: Azure Tablo Depolaması 'nda lider yönetimini yapılandırın.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: 21105d72ccd288faf0fed58019e67afe2e1c9d01
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825281"
---
# <a name="lead-management-instructions-for-table-storage"></a>Tablo depolama için öncü yönetim yönergeleri

Bu makalede, satış fırsatlarını yönetmek için Azure Tablo depolamanın nasıl yapılandırılacağı açıklanır. Tablo depolama, müşteri bilgilerini depolamanıza ve değiştirmenize yardımcı olur.

## <a name="configure-table-storage"></a>Tablo Depolamayı Yapılandırma

1. Azure hesabınız yoksa [ücretsiz bir deneme hesabı oluşturun](https://azure.microsoft.com/pricing/free-trial/).
1. Hesabınız etkin olduktan sonra [Azure Portal](https://portal.azure.com)oturum açın.
1. Azure portal, aşağıdaki adımları izleyin:  
    1. Sol taraftaki bölmede **+ kaynak oluştur** ' u seçin. **Yeni** bölme açılır.
    1. **Yeni** bölmesinde **depolama**' yı seçin. **Öne çıkan** bir liste, sağ tarafta açılır.
    1. **Depolama hesabı**' nı seçin. Ardından, [depolama hesabı oluşturma](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)bölümündeki yönergeleri izleyin.

    ![Azure Storage hesabı oluşturma](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Depolama hesapları hakkında daha fazla bilgi için bkz. [hızlı başlangıç öğreticileri](https://docs.microsoft.com/azure/storage/). Fiyatlandırma bilgileri için bkz. [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

1. Depolama Hesabınız sağlanana kadar bekleyin, genellikle birkaç dakika sürer. Sonra, Azure portal giriş sayfasından hesaba erişin: **Tüm kaynaklarınızı** veya gezinti bölmesindeki **tüm kaynakları** görüntüle ' yi seçin.

    ![Azure depolama hesabınıza erişin](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. Depolama hesabı bölmesinizden anahtar için depolama hesabı bağlantı dizesini kopyalayın. Bulut İş Ortağı Portalı depolama hesabının **bağlantı dizesi** alanına yapıştırın.

    Örnek bağlantı dizesi:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure depolama anahtarı](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Tablo depolamadaki verileri görmek için [Azure Depolama Gezgini](https://azurestorageexplorer.codeplex.com/) veya benzer bir araç kullanabilirsiniz. Ayrıca, verileri dosyadan dışarı aktarabilirsiniz.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Tablo depolama ile Microsoft Flow kullanma (*isteğe bağlı*)

Tablo depolamaya bir müşteri adayı eklendiğinde otomatik olarak bildirim göndermek için [Microsoft Flow](https://docs.microsoft.com/flow/) kullanabilirsiniz. Microsoft Flow hesabınız yoksa [ücretsiz hesap için kaydolun](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Müşteri adayı bildirimi örneği

Bu örnek, temel akışın nasıl oluşturulacağını gösterir. Akış, yeni müşteri adayları tablo depolamaya eklendiğinde otomatik olarak bir e-posta bildirimi gönderir.

1. Microsoft Flow hesabınızda oturum açın.
1. Sol taraftaki Gezinti bölmesinde **Akışlarım**' ı seçin.
1. Üst gezinti çubuğunda **+ Yeni**' yi seçin.  
1. Aşağı açılan listeden **+ boş oluştur**' u seçin.
1. **Boş akış oluştur**altında, **boş oluştur**' u seçin.

   ![Boş yeni bir akış oluşturun](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Bağlayıcılar ve Tetikleyiciler arama sayfasında **Tetikleyiciler**' i seçin.
1. **Tetikleyiciler**altında **yinelenme**' yi seçin.
1. **Yineleme** penceresinde, **Aralık**için varsayılan **1** ayarını tutun. **Sıklık** açılan listesinden **saat**' i seçin.

   >[!NOTE] 
   >Bu örnek bir saatlik aralığı kullanır. Ancak, iş gereksinimlerinize en uygun bir Aralık ve sıklık seçebilirsiniz.

   ![Yineleme için 1 saatlik sıklık ayarla](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. **+ Yeni adım**' ı seçin.
1. **Geçmiş zamanı al**' ı arayın ve sonra **bir eylem seçin**altında **geçmiş zamanı al** ' ı seçin.

    !["Geçmiş zamanı al" eylemini bulun ve seçin](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. **Son zaman al** penceresinde, **aralığı** **1**olarak ayarlayın.  **Zaman birimi** aşağı açılan listesinden **saat**' i seçin.
    >[!IMPORTANT] 
    >**Aralık** ve **zaman biriminin** yineleme için yapılandırdığınız Aralık ve sıklık ile eşleştiğinden emin olun (adım 8).

    ![Son tarihi al zaman aralığını ayarla](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Her adımın doğru yapılandırıldığını doğrulamak için akışınızı dilediğiniz zaman kontrol edebilirsiniz: Flow menü çubuğundan **Flow denetleyicisi** ' ni seçin.

Sonraki adımlarda, depolama tablonuza bağlanır ve yeni müşteri adaylarını işlemek için işleme mantığını ayarlarsınız.

1. **Geçmiş zamanı al** adımından sonra **+ yeni adım**' ı seçin ve ardından **varlıkları al**' ı arayın.
1. **Eylemler**altında **varlıkları al**' ı seçin ve ardından **Gelişmiş seçenekleri göster**' i seçin.
1. **Varlıkları al** penceresinde aşağıdaki alanları girin:

   - **Tablo**: tablo depolamanın adı. Aşağıdaki görüntüde "Marketplacefırsatlarını" girilen bir gösterilmektedir:

     ![Azure Tablo adı için özel bir değer seçin](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Filtre sorgusu**: bu alanı seçtiğinizde, **son zaman al** simgesi açılır pencerede görüntülenir. Sorguyu filtrelemek için bu değeri bir zaman damgası olarak kullanmak üzere **geçmiş zamanı** seçin. Veya, alanına aşağıdaki işlevi yapıştırabilirsiniz:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Filtre sorgusu işlevini ayarlama](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Yeni müşteri adayları için tablo depolama alanınızı taramak üzere koşul eklemek için **yeni adım** ' ı seçin.

   ![Tablo depolama alanını taramak üzere bir koşul eklemek için "yeni adım" kullanın](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. **Eylem seçin** penceresinde **Eylemler**' i ve ardından **koşul denetimi**' ni seçin.

     ![Koşul denetimi ekleme](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. **Koşul** penceresinde, **bir değer seçin**' i seçin ve ardından açılır pencerede **ifade** ' ı seçin.
1. `length(body('Get_entities')?['value'])` ***FX*** alanına yapıştırın. Bu işlevi eklemek için **Tamam ' ı** seçin. 



     ![Koşula işlev ekleme](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Koşulun sonucuna göre gerçekleştirilecek eylemi ayarlayın.

    1. Aşağı açılan listeden **büyüktür** ' i seçin.
   1. Değer olarak **0** girin.

     ![Koşul sonuçlarına göre bir eylem ayarlama](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Koşul "Eğer hayır" olarak çözümlenirse hiçbir şey yapmayın.

    Koşul "If Yes" olarak çözümlenirse, Office 365 hesabınızı bağlayan bir eylemi bir e-posta göndermek üzere tetikleyin:
   1. **Eylem Ekle**' yi seçin.
   1. **E-posta gönder**' i seçin.
   1. **E-posta gönder** penceresinde, aşağıdaki alanlara bilgi girin:

      - **Kime**: bildirimi alacak herkese yönelik bir e-posta adresi.
      - **Konu**: e-posta konusu. Örneğin: *yeni müşteri adayları!*
      - **Gövde**: her bir e-postaya dahil etmek istediğiniz metin (isteğe bağlı). Ayrıca, müşteri adayı bilgilerini eklemek için `body('Get_entities')?['value']` bir işlev olarak yapıştırın.

        >[!NOTE] 
        >E-postanın gövdesine ek statik veya dinamik veri noktaları ekleyebilirsiniz.

      ![Müşteri adayı bildirimi için e-posta ayarla](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Akışı kaydetmek için **Kaydet** ' i seçin. Microsoft Flow hatalar için otomatik olarak test eder. Herhangi bir hata yoksa, akışınız kaydedildikten sonra çalışmaya başlar.

    Aşağıdaki görüntüde, son akışın nasıl görüneceğine ilişkin bir örnek gösterilmektedir.

    [![son akış sırası](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Büyütmek için görüntüyü seçin.* )

### <a name="manage-your-flow"></a>Akışınızı yönetin

Kaydettikten sonra akışınızı yönetmek kolaydır. Akışınız üzerinde tamamen denetim sahibi olursunuz. Örneğin, bunu durdurabilir, düzenleyebilir, çalıştırma geçmişine bakabilirsiniz ve analiz alabilirsiniz. Aşağıdaki görüntüde, akış yönetimi seçenekleri gösterilmektedir.

 ![Akış yönetimi seçenekleri](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Akış, **akışı kapat**seçeneğini seçinceye kadar çalışmaya devam eder.

Hiçbir müşteri adayı e-posta bildirimi almıyorsanız, tablo depolamaya yeni müşteri adayları eklenmez.
Bir akış hatası oluşursa aşağıdaki örnekteki gibi bir e-posta alacaksınız:

 ![Flow hata e-posta bildirimi](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Sonraki adımlar

[Müşteri adaylarını yapılandırma](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
