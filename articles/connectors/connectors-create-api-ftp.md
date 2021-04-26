---
title: FTP sunucusuna bağlan
description: Azure Logic Apps kullanarak FTP sunucusu üzerinde dosya oluşturan, izleyen ve yöneten görevleri ve iş akışlarını otomatikleştirin
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: f4cad2b658547d56d00efdd5e1496110f8e4a5e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999595"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak FTP dosyalarını oluşturma, izleme ve yönetme

Azure Logic Apps ve FTP Bağlayıcısı sayesinde, FTP sunucusundaki hesabınız aracılığıyla dosya oluşturan, izleyen, gönderen ve alan otomatik görevler ve iş akışları oluşturabilirsiniz, örneğin:

* Dosya eklendiğinde veya değiştirildiğinde izleyin.
* Dosyaları alın, oluşturun, kopyalayın, güncelleştirin, listeleyin ve silin.
* Dosya içeriğini ve meta verileri alın.
* Arşivi klasörlere ayıklayın.

FTP sunucusundan yanıt alan Tetikleyicileri kullanabilir ve çıktıyı diğer eylemler için kullanılabilir hale getirebilirsiniz. FTP sunucunuzdaki dosyaları yönetmek için mantıksal uygulamalarınızda çalıştırma eylemlerini kullanabilirsiniz. Ayrıca, diğer eylemlerdeki çıktıyı FTP eylemleriyle kullanmasını sağlayabilirsiniz. Örneğin, FTP sunucusundan düzenli olarak dosya alırsanız, Office 365 Outlook bağlayıcısını veya Outlook.com bağlayıcısını kullanarak bu dosyalar ve bunların içerikleri hakkında e-posta gönderebilirsiniz. Logic Apps 'e yeni başladıysanız [Azure Logic Apps ne olduğunu](../logic-apps/logic-apps-overview.md)gözden geçirin.

## <a name="limitations"></a>Sınırlamalar

* FTP Bağlayıcısı, yalnızca TLS/SSL üzerinden açık FTP 'yi (FTPS) destekler ve örtük FTPS ile uyumlu değildir.

* Varsayılan olarak, FTP eylemleri *50 MB veya daha küçük* olan dosyaları okuyabilir veya yazabilir. 50 MB 'tan büyük dosyaları işlemek için FTP eylemleri [ileti parçalama](../logic-apps/logic-apps-handle-large-messages.md)desteği sağlar. **Dosya Içeriğini al** eylemi örtük olarak parçalama kullanır.

* FTP Tetikleyicileri parçalama desteklemez. Dosya içeriği istenirken Tetikleyiciler yalnızca 50 MB veya daha küçük olan dosyaları seçer. 50 MB 'tan büyük dosyaları almak için şu modele uyun:

  * Dosya **ekleme veya değiştirme (yalnızca Özellikler)** gibi dosya özelliklerini döndüren bir FTP tetikleyicisi kullanın.

  * Tam dosyayı okuyan ve örtük olarak parçalama kullanan FTP **Dosya Içeriğini al** eylemiyle tetikleyiciyi izleyin.

* Şirket içi bir FTP sunucunuz varsa, bir [tümleştirme hizmeti ortamı (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) oluşturmayı veya [karma bağlantı Azure App Service](../app-service/app-service-hybrid-connections.md)kullanmayı düşünün. Bu, her ikisi de şirket içi veri ağ geçidi kullanmadan şirket içi veri kaynaklarına erişmenize olanak tanır.

## <a name="how-ftp-triggers-work"></a>FTP Tetikleyicileri nasıl çalışır?

FTP Tetikleyicileri, FTP dosya sistemini yoklayarak ve Son yoklamadan bu yana değiştirilen herhangi bir dosyayı arayarak çalışır. Bazı araçlar, dosyalar değiştiğinde zaman damgasını korumanıza olanak sağlar. Bu durumlarda, tetikleyicinizin çalışabilmesi için bu özelliği devre dışı bırakmanız gerekir. Yaygın olarak kullanılan bazı ayarlar şunlardır:

| SFTP istemcisi | Eylem |
|-------------|--------|
| WinSCP | **Seçenekler**  >  **Tercihler**  >  **Aktarım**  >  **düzenleme**  >  **zaman damgası**  >  **devre dışı bırak** ' a gidin |
| FileZilla | **Aktarım**' a git  >  **aktarılan dosyaların zaman damgalarını koru**  >  **devre dışı bırak** |
|||

Tetikleyici yeni bir dosya bulduğunda, tetikleyici yeni dosyanın tamamlandığını ve kısmen yazılmadığını denetler. Örneğin, tetikleyici dosya sunucusunu denetlerken bir dosya sürmekte olan değişiklikler olabilir. Kısmen yazılmış bir dosyanın döndürülmemek için tetikleyici, son değişiklikleri olan dosyanın zaman damgasını Not etmez, ancak bu dosyayı hemen döndürmez. Tetikleyici dosyayı yalnızca sunucuyu yoklayarak geri döndürür. Bazen bu davranış, tetikleyicinin yoklama aralığı iki katına varan bir gecikmeye neden olabilir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* FTP ana bilgisayar sunucunuzun adresi ve hesap kimlik bilgileri

  FTP Bağlayıcısı, FTP sunucunuza internet 'ten erişilebildiğinden ve *Pasif* modda çalışacak şekilde ayarlanmış olmasını gerektirir. Kimlik bilgileriniz mantıksal uygulamanızın bağlantı oluşturmasına ve FTP hesabınıza erişmesine izin verir.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* FTP hesabınıza erişmek istediğiniz mantıksal uygulama. Bir FTP tetikleyicisiyle başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Bir FTP eylemi kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="connect-to-ftp"></a>FTP 'ye bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Boş Logic Apps için, arama kutusuna `ftp` filtreniz olarak girin. **Tetikleyiciler** listesinden istediğiniz tetikleyiciyi seçin.

   -veya-

   Mevcut Logic Apps için, eylem eklemek istediğiniz son adım altında **yeni adım**' ı seçin ve ardından **Eylem Ekle**' yi seçin. Arama kutusuna `ftp` filtreniz olarak yazın. **Eylemler** listesinden istediğiniz eylemi seçin.

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Bağlantı bilgilerinizi girip **Oluştur**' u seçin.

1. Seçtiğiniz tetikleyici veya eyleminiz için bilgi sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="examples"></a>Örnekler

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>FTP tetikleyicisi Ekle

**Bir dosya eklendiğinde veya değiştirildiğinde (yalnızca Özellikler)** TETIKLEYICI bir FTP sunucusunda bir dosyanın eklendiğini veya değiştirildiğini algıladığında bir mantıksal uygulama iş akışı başlatır. Örneğin, dosyanın içeriğini denetleyen ve içeriğin belirtilen bir koşulu karşılayıp karşılamadığını temel alarak bu içeriği almak için karar veren bir koşul ekleyebilirsiniz. Son olarak, dosyanın içeriğini alan ve bu içeriği SFTP sunucusundaki farklı bir klasöre koyabileceğiniz bir eylem ekleyebilirsiniz.

Örneğin, bu tetikleyiciyi, müşteri siparişlerini tanımlayan yeni dosyalar için FTP klasörünü izlemek üzere kullanabilirsiniz. Daha sonra bu yeni dosyanın özelliklerini almak için **dosya meta verilerini al** gıbı bir FTP eylemi kullanabilir ve daha sonra işleme için dosya **içeriğini al** ' ı kullanarak bu siparişi bir Siparişler veritabanında saklayabilirsiniz.

**Bir dosya eklendiğinde veya değiştirildiğinde (yalnızca Özellikler)** tetikleyicisinin nasıl kullanılacağını gösteren bir örnek aşağıda verilmiştir.

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Boş Logic Apps için, arama kutusuna `ftp` filtreniz olarak girin. Tetikleyiciler listesinde, bu tetikleyiciyi seçin: **bir dosyalanmış eklendiğinde veya değiştirildiğinde (yalnızca Özellikler)**

   ![FTP tetikleyicisini bul ve Seç](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Bağlantınız için gerekli ayrıntıları sağlayın ve **Oluştur**' u seçin.

   Varsayılan olarak, bu bağlayıcı dosyaları metin biçiminde aktarır. Dosyaları ikili biçimde (örneğin, kodlama kullanıldığı yerde) aktarmak için **Ikili taşıma**' yı seçin.

   ![FTP sunucusuna bağlantı oluştur](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. **Klasör** kutusunda, bir listenin görünmesi için klasör simgesini seçin. Yeni veya düzenlenmiş dosyalar için izlemek istediğiniz klasörü bulmak için, doğru açılı oku ( **>** ) seçin, bu klasöre gidin ve sonra klasörü seçin.

   ![İzlenecek klasörü bul ve Seç](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   Seçtiğiniz klasör **klasör** kutusunda görünür.

   ![Seçili klasör "Folder" özelliğinde görünüyor](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Artık mantıksal uygulamanızın bir tetikleyicisi olduğuna göre, mantıksal uygulamanız yeni veya düzenlenmiş bir dosya bulduğunda çalıştırmak istediğiniz eylemleri ekleyin. Bu örnekte, yeni veya güncelleştirilmiş içeriği alan bir FTP eylemi ekleyebilirsiniz.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>FTP eylemi Ekle

**Dosya meta verilerini al** EYLEMI, FTP sunucunuzdaki bir dosyanın özelliklerini alır ve **dosya içeriğini al** eylemi, FTP sunucunuzdaki bu dosya hakkındaki bilgilere göre dosya içeriğini alır. Örneğin, bu dosya eklendikten veya düzenlendikten sonra dosyanın içeriğini almak için önceki örnekteki tetikleyiciyi ve bu eylemleri ekleyebilirsiniz.

1. Tetikleyici veya başka herhangi bir eylem altında **yeni adım**' ı seçin.

1. Arama kutusuna `ftp` filtreniz olarak yazın. Eylemler listesi altında şu eylemi seçin: **dosya meta verilerini al**

   !["Dosya meta verilerini al" eylemini seçin](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Zaten bir FTP sunucunuz ve hesabınızla bağlantınız varsa, sonraki adıma geçin. Aksi takdirde, bu bağlantı için gerekli ayrıntıları sağlayın ve **Oluştur**' u seçin.

   ![FTP sunucusu bağlantısı oluştur](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. **Dosya meta verilerini al** eylemi görüntülendikten sonra, dinamik içerik listesinin görünmesi için **Dosya** kutusunun içine tıklayın. Artık önceki adımlardan çıktılar için özellikler seçebilirsiniz. Dinamik içerik listesinde **dosya meta verilerini al**' ın altında, dosyanın eklendiği veya güncelleştirildiği koleksiyona başvuran dosya **kimliği özelliği listesini** seçin.

   !["Dosya kimliği listesi" özelliği bul ve Seç](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   Dosya **kimliği** özelliği artık **Dosya** kutusunda görünür.

   ![Seçili "dosya kimliği listesi" özelliği](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Şimdi şu FTP eylemini ekleyin: **Dosya Içeriğini al**

   !["Dosya içeriğini al" eylemini bulun ve seçin](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. **Dosya Içeriğini al** eylemi görüntülendikten sonra, dinamik içerik listesinin görünmesi için **Dosya** kutusunun içine tıklayın. Artık önceki adımlardan çıktılar için özellikler seçebilirsiniz. Dinamik içerik listesinde, **dosya meta verilerini al**' ın altında, eklenen veya güncellenen dosyaya başvuran **ID** özelliğini seçin.

   !["Kimlik" özelliği bul ve Seç](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   **ID** özelliği artık **Dosya** kutusunda görünür.

   ![Seçili "ID" özelliği](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Mantıksal uygulamanızı kaydedin.

## <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

İş akışınızın, istediğiniz içeriği döndürdüğünü denetlemek için karşıya yüklenen veya güncelleştirilmiş dosyadan içerik gönderen başka bir eylem ekleyin.

1. **Dosya Içeriğini al** eyleminin altında, size dosyanın içeriğini gönderebilmeniz için bir eylem ekleyin. Bu örnek, Office 365 Outlook için **e-posta gönder** eylemini ekler.

   ![E-posta göndermek için bir eylem ekleyin](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Eylem göründükten sonra, bilgileri sağlayın ve test etmek istediğiniz özellikleri ekleyin. Örneğin, **Dosya Içeriğini al** bölümünde **daha fazla göster** ' i seçtikten sonra dinamik içerik listesinde görünen **dosya içeriği** özelliğini ekleyin.

   ![E-posta eylemi hakkında bilgi sağlayın](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Mantıksal uygulamanızı kaydedin. Mantıksal uygulamayı çalıştırmak ve tetiklemek için, araç çubuğunda **Çalıştır**' ı seçin ve ardından mantıksal UYGULAMANıZıN izlediği FTP klasörüne bir dosya ekleyin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bu bağlayıcı hakkında, bağlayıcının Swagger dosyasında açıklanan Tetikleyiciler, Eylemler ve sınırlar gibi daha teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/ftpconnector/)bakın.

> [!NOTE]
> Bir [tümleştirme hizmeti ortamındaki (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Logic Apps için, bu bağlayıcının Ise etiketli sürümü bunun yerine [Ise ileti sınırlarını](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) kullanır.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin

