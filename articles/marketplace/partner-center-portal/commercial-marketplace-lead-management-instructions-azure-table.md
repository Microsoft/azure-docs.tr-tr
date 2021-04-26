---
title: Azure Tablo depolama ile lider Yönetimi-Microsoft ticari Market
description: Azure Tablo Depolamayı kullanarak Microsoft AppSource ve Azure Marketi için müşteri adaylarını yapılandırma hakkında bilgi edinin
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 08/25/2020
ms.openlocfilehash: 3c8b9444344e5bae414145e2b3367c265114a423
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586852"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>Azure Tablo Depolamayı kullanarak ticari Market müşteri adaylarını yönetme

Müşteri ilişkileri yönetimi (CRM) sisteminiz Iş Ortağı Merkezi 'nde Microsoft AppSource ve Azure Market müşteri adaylarını almak üzere açıkça desteklenmiyorsa, bu müşteri adaylarını işlemek için Azure Tablo depolamayı kullanabilirsiniz. Sonra verileri dışarı aktarıp CRM sisteminize aktarabilirsiniz. Bu makalede, bir Azure depolama hesabının ve bu hesap altında bir tablonun nasıl oluşturulacağı açıklanmaktadır. Ayrıca, teklifiniz bir müşteri adayı aldığında e-posta bildirimi göndermek için Power otomatikleştirmek ' nı kullanarak yeni bir akış oluşturabilirsiniz.

## <a name="configure-an-azure-storage-account"></a>Azure depolama hesabı yapılandırma

1. Azure hesabınız yoksa [ücretsiz bir deneme hesabı oluşturabilirsiniz](https://azure.microsoft.com/pricing/free-trial/).
1. Azure hesabınız etkin olduktan sonra [Azure Portal](https://portal.azure.com)oturum açın.
1. Azure portal, aşağıdaki yordamı kullanarak bir depolama hesabı oluşturun:

    1. Sol menü çubuğunda **+ kaynak oluştur** ' u seçin. **Yeni** bölme sağ tarafta görüntülenir.
    1. **Yeni** bölmesinde **depolama** ' yı seçin. **Öne çıkan** bir liste sağ tarafta görüntülenir.
    1. Hesap oluşturmaya başlamak için **depolama hesabı** ' nı seçin. [Depolama hesabı oluşturma](../../storage/common/storage-account-create.md?tabs=azure-portal)bölümündeki yönergeleri izleyin.

        :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png" alt-text="Azure depolama hesabı oluşturma adımları.":::

        Depolama hesapları hakkında daha fazla bilgi için bkz. [Hızlı Başlangıç Öğreticisi](../../storage/index.yml). Depolama fiyatlandırması hakkında daha fazla bilgi için bkz. [Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

1. Depolama Hesabınız sağlanana kadar bekleyin. Bu işlem genellikle birkaç dakika sürer.

## <a name="create-a-table-in-your-storage-account"></a>Depolama hesabınızda bir tablo oluşturun

1. Azure portal **ana** sayfasında, depolama hesabınıza erişmek için **Tüm kaynaklarınızı gör** ' ü seçin. Ayrıca, Azure portal sol menü çubuğundan **tüm kaynaklar** ' ı da seçebilirsiniz.

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png" alt-text="Azure depolama hesabınıza erişin.":::

1. Depolama hesabı bölmesinizden **erişim anahtarları** ' nı seçin ve anahtar için **bağlantı dizesi** değerini kopyalayın. Azure Market teklifiniz için müşteri adaylarını almak üzere Yayımlama portalında sağlamanız gereken **depolama hesabı bağlantı dizesi** değeri olduğundan bu değeri kaydedin.

    Bağlantı dizesine bir örnek aşağıda verilmiştir.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png" alt-text="Azure depolama anahtarı.":::


1. Depolama hesabı bölmesinizden **Tablolar**' ı seçin ve tablo oluşturmak Için **+ tablo** ' yı seçin. Tablonuz için bir ad girin ve **Tamam**' ı seçin. Müşteri adayları alındığında e-posta bildirimleri almak üzere bir akış yapılandırmak istiyorsanız bu değeri kaydedin.

    ![Azure tabloları](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Depolama tablonuzdaki verileri görmek için [Azure Depolama Gezgini](https://www.storageexplorer.com) veya başka bir aracı kullanabilirsiniz. Azure tablosundaki verileri de dışarı aktarabilirsiniz.

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>Seçim Lider bildirimleri almak için güç otomatikleştirmesini kullanma

Azure depolama tablonuza bir müşteri adayı her eklendiğinde bildirimleri otomatikleştirmek için [Güç otomatikleştirmesini](/flow/) kullanabilirsiniz. Hesabınız yoksa [ücretsiz bir hesap için kaydolabilirsiniz](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Müşteri adayı bildirimi örneği

Örnek, Azure Tablo depolama alanına yeni bir müşteri adayı eklendiğinde otomatik olarak bir e-posta bildirimi gönderen bir akış oluşturur. Bu örnek, tablo depolaması güncelleştirilirse her saat için müşteri adayı bilgilerini göndermek üzere bir yinelenme belirler.

1. Power otomatikleştir hesabınızda oturum açın.
1. Sol taraftaki çubukta **Akışlarım**' ı seçin.
1. Üstteki çubukta **+ Yeni**' yi seçin.
1. Açılan listede **+ zamanlandı**' i seçin (boş).

   ![Akışlarım + zamanlandığı--boş](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. **Zamanlanan akış oluştur** penceresinde, **yineleme** için, sıklık için Aralık ve **saat** için **1** ' i seçin. Ayrıca, isterseniz akışa bir ad verin. **Oluştur**’u seçin.

   >[!NOTE]
   >Bu örnek bir saatlik aralığı kullansa da, iş gereksinimleriniz için en iyi aralığı ve sıklığı seçebilirsiniz.

   ![Zamanlanan akış oluşturma](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. **+ Yeni adım**’ı seçin.
1. **Eylem seçin** penceresinde, **geçmişteki zamanı al**' ı arayın. **Eylemler** altında, **geçmiş zamanı al**' ı seçin.

   ![Eylem seçin](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. **Son zaman al** penceresinde **aralığı** **1** olarak ayarlayın. **Zaman birimi** aşağı açılan listesinden **saat**' i seçin.

    >[!IMPORTANT]
    >8. adımda gönderdiğiniz aralığın ve zaman biriminin 5. adımda yinelenme için yapılandırdığınız Aralık ve sıklık ile eşleştiğinden emin olun.

    ![Geçmiş zaman aralığını ayarla](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP]
   >Her adımın doğru yapılandırıldığını doğrulamak için akışınızı dilediğiniz zaman kontrol edebilirsiniz. **Akışınızı denetlemek için akış menü** çubuğundan **Flow denetleyicisi** ' ni seçin.

   Sonraki adımlarda, tablonuza bağlanır ve yeni müşteri adaylarını işlemek için işleme mantığını ayarlarsınız.

1. **+ Yeni adım**’ı seçin. Sonra **bir eylem seçin** penceresinde **varlıkları al** ' ı arayın.
1. **Eylemler** altında **varlıkları al (Azure Tablo Depolama)** seçeneğini belirleyin.
1. **Azure Tablo Depolaması** penceresinde, aşağıdaki kutulara bilgi sağlayın ve **Oluştur**' u seçin:

    * **Bağlantı adı**: Bu akış ve tablo arasında oluşturduğunuz bağlantı için anlamlı bir ad sağlayın.
    * **Depolama hesabı adı**: tablonuzun depolama hesabının adını belirtin. Bu adı, depolama hesabının **erişim anahtarları** sayfasında bulabilirsiniz.
    * **Paylaşılan depolama anahtarı**: tablonuz için Mağaza hesabınız için anahtar değerini sağlayın. Bu değeri, depolama hesabının **erişim anahtarları** sayfasında bulabilirsiniz.

      ![Azure Tablo depolama penceresi](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   **Oluştur**' u seçtikten sonra **varlıkları al** penceresi görüntülenir. Burada, **Gelişmiş seçenekleri göster**' i seçin ve aşağıdaki kutulara bilgi sağlayın:

   * **Tablo**: tablonuzun adını seçin ( [tablo oluştur](#create-a-table-in-your-storage-account)' dan). Aşağıdaki görüntüde `marketplaceleads` Bu örnek için tablo seçildiğinde istem gösterilmektedir.

     ![Varlıkları al penceresi](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Filtre sorgusu**: Bu kutuyu seçin ve bu işlevi kutuya yapıştırın: `Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Varlıkları al, filtre sorgusu kutusu](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Azure tablosu bağlantısını ayarlamayı tamamladığınıza göre, yeni müşteri adayları için Azure tablosunu taramak üzere koşul eklemek üzere **yeni adım** ' ı seçin.

1. **Eylem seçin** penceresinde **Eylemler**' i seçin. Sonra **koşul denetimi**' ni seçin.

    ![Bir eylem penceresi seçin](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. **Koşul** penceresinde, **bir değer seçin**' i seçin. Ardından açılır pencerede **ifade** ' ı seçin.

1. `length(body('Get_entities')?['value'])` **FX** kutusuna yapıştırın. Bu işlevi eklemek için **Tamam ' ı** seçin.

1. Koşulu ayarlamayı tamamlaması için:
    1. Aşağı açılan listeden **büyüktür** ' i seçin.
    2. Değer olarak **0** girin.

        ![Koşul penceresi](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   Sonraki birkaç adımda, koşulun sonucuna göre gerçekleştirilecek eylemi ayarlarsınız:

   * Koşul **Hayır** olarak çözümlenirse, hiçbir şey yapmayın.
   * Koşul **Evet ise** olarak çözümlenirse, e-posta göndermek için iş veya okul hesabınızı bağlayan bir eylem tetikleyin. 

1. **Evet ise** **Eylem Ekle** ' yi seçin.

    ![Koşul penceresi, Evet Ise, eylem ekleme](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. **E-posta gönder (Office 365 Outlook)** seçeneğini belirleyin.

    ![Koşul penceresi, Evet Ise e-posta gönder](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Farklı bir e-posta sağlayıcısı kullanmak için, bunun yerine eylem olarak **bir e-posta bildirimi (posta) Gönder** ' i arayın ve seçin. Yönergeler Office 365 Outlook kullanarak nasıl yapılandırılacağını gösterir, ancak yönergeler farklı bir e-posta sağlayıcısına benzer.

1. Office 365 Outlook penceresinde, aşağıdaki kutulara bilgi sağlayın:

    1. **Kime**: Bu bildirimi alacak herkes için bir e-posta adresi girin.
    1. **Konu**: e-posta için bir konu belirtin. **Yeni müşteri adayları** bir örnektir!
    1. **Gövde**: her bir e-postaya eklemek istediğiniz metni ekleyin (isteğe bağlı) ve ardından içine yapıştırın `body('Get_entities')?['value']` .

    >[!NOTE]
    >Bu e-postanın gövdesine ek statik veya dinamik veri noktaları ekleyebilirsiniz.

    ![Durum penceresi, Evet Ise Office 365 Outlook penceresi](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Akışı kaydetmek için **Kaydet**’i seçin. Power otomatikleştir, akışı otomatik olarak hatalara karşı sınar. Herhangi bir hata yoksa, akışınız kaydedildikten sonra çalışmaya başlar.

Aşağıdaki görüntüde, son akışın nasıl görüneceğine ilişkin bir örnek gösterilmektedir.

![Son akışa bir örnek](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Akışınızı yönetin

Flow 'un çalışmaya başladıktan sonra kolayca yönetilmesi kolaydır. Akışınız üzerinde tamamen denetim sahibi olursunuz. Örneğin, bunu durdurabilir, düzenleyebilir, çalıştırma geçmişine bakabilirsiniz ve analiz alabilirsiniz. Aşağıdaki görüntüde, bir akışı yönetmek için kullanılabilecek seçenekler gösterilmektedir.

 ![Akışı yönetme](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Akış, **devre dışı bırak** seçeneğini kullanarak durduruncaya kadar çalışmaya devam eder.

Hiçbir müşteri adayı e-posta bildirimi almıyorsanız, Azure tablosuna yeni müşteri adayları eklenmemiş demektir. Flow hatalarıyla karşılaşırsanız, bu örnekte olduğu gibi bir e-posta alırsınız.

 ![Flow hata e-posta bildirimi](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Teklifinizi Azure tablosuna müşteri adayları gönderecek şekilde yapılandırma

Yayımlama portalında teklifiniz için lider yönetimi bilgilerini yapılandırmaya hazırsanız, bu adımları izleyin.

1. Teklifiniz için **teklif kurulumu** sayfasına gidin.

1. **Müşteri müşteri adayları** bölümünde **Bağlan**' ı seçin.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="Müşteri liderleri":::

1. **Bağlantı ayrıntıları** açılır penceresinde, **müşteri adayı hedefi** için **Azure tablosu** ' nu seçin. 
     ![Lider yönetimi, bağlantı ayrıntıları](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. **Depolama hesabı bağlantı dizesi** kutusunda önceki adımları Izleyerek oluşturduğunuz Azure depolama hesabındaki bağlantı dizesini yapıştırın.
     ![Lider yönetimi, bağlantı ayrıntıları depolama hesabı](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **İletişim e-postası**: şirketinizdeki kişiler için yeni bir müşteri adayı alındığında e-posta bildirimleri alması gereken e-postalar sağlayın. Birden çok e-postayı noktalı virgülle ayırarak sağlayabilirsiniz.

1. **Tamam**’ı seçin.

Bir müşteri adayı hedefine başarıyla bağlandığınızdan emin olmak için **Doğrula** düğmesini seçin. Başarılı olursa, müşteri adayı hedefinde bir test liderine sahip olacaksınız.

>[!NOTE]
>Teklif için müşteri adaylarını almadan önce teklifin geri kalanını yapılandırmayı ve bunu yayımlamanız gerekir.

Müşteri adayları oluşturulduğunda, Microsoft müşteri adaylarını Azure tablosuna gönderir. Bir akış yapılandırdıysanız, yapılandırdığınız e-posta adresine de bir e-posta gönderilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Müşteri adayı yönetimi hakkında SSS ve sorun giderme](../lead-management-faq.md)
