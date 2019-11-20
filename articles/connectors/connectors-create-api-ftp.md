---
title: FTP sunucusuna bağlan-Azure Logic Apps
description: Azure Logic Apps ile FTP sunucusu üzerinde dosya oluşturma, izleme ve yönetme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: divswa, klam, LADocs
ms.topic: conceptual
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a73fad3097be73e01a7a2a6652129cd7c9db9555
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050970"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak FTP dosyaları oluşturun, izleyin ve yönetin

Azure Logic Apps ve FTP Bağlayıcısı sayesinde, FTP sunucusundaki hesabınız aracılığıyla dosya oluşturan, izleyen, gönderen ve alan otomatik görevler ve iş akışları oluşturabilirsiniz, örneğin:

* Dosya eklendiğinde veya değiştirildiğinde izleyin.
* Dosyaları alın, oluşturun, kopyalayın, güncelleştirin, listeleyin ve silin.
* Dosya içeriğini ve meta verileri alın.
* Arşivi klasörlere ayıklayın.

FTP sunucusundan yanıt alan Tetikleyicileri kullanabilir ve çıktıyı diğer eylemler için kullanılabilir hale getirebilirsiniz. FTP sunucunuzdaki dosyaları yönetmek için mantıksal uygulamalarınızda çalıştırma eylemlerini kullanabilirsiniz. Ayrıca, diğer eylemlerdeki çıktıyı FTP eylemleriyle kullanmasını sağlayabilirsiniz. Örneğin, FTP sunucusundan düzenli olarak dosya alırsanız, Office 365 Outlook bağlayıcısını veya Outlook.com bağlayıcısını kullanarak bu dosyalar ve bunların içerikleri hakkında e-posta gönderebilirsiniz. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="limits"></a>Sınırlar

* FTP Bağlayıcısı SSL üzerinden yalnızca açık FTP (FTPS) destekler ve örtük FTPS ile uyumlu değildir.

* Varsayılan olarak, FTP eylemleri *50 MB veya daha küçük*olan dosyaları okuyabilir veya yazabilir. 50 MB 'tan büyük dosyaları işlemek için FTP eylemleri [ileti parçalama](../logic-apps/logic-apps-handle-large-messages.md)desteği sağlar. **Dosya Içeriğini al** eylemi örtük olarak parçalama kullanır.

* FTP Tetikleyicileri parçalama desteklemez. Dosya içeriği istenirken Tetikleyiciler yalnızca 50 MB veya daha küçük olan dosyaları seçer. 50 MB 'tan büyük dosyaları almak için şu modele uyun:

  * Dosya **ekleme veya değiştirme (yalnızca Özellikler)** gibi dosya özelliklerini döndüren bir FTP tetikleyicisi kullanın.

  * Tam dosyayı okuyan ve örtük olarak parçalama kullanan FTP **Dosya Içeriğini al** eylemiyle tetikleyiciyi izleyin.

## <a name="how-ftp-triggers-work"></a>FTP Tetikleyicileri nasıl çalışır?

FTP Tetikleyicileri, FTP dosya sistemini yoklayarak ve Son yoklamadan bu yana değiştirilen herhangi bir dosyayı arayarak çalışır. Bazı araçlar, dosyalar değiştiğinde zaman damgasını korumanıza olanak sağlar. Bu durumlarda, tetikleyicinizin çalışabilmesi için bu özelliği devre dışı bırakmanız gerekir. Yaygın olarak kullanılan bazı ayarlar şunlardır:

| SFTP istemcisi | Action |
|-------------|--------|
| WinSCP | **Seçenekler** Tercihler aktarım düzenleme zaman damgasıdevredışıbırak'agidin >  >  >  >  >  |
| FileZilla |  > Aktarım > ' a git**aktarılan dosyaların zaman damgalarını koru** **devre dışı bırak** |
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

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Boş Logic Apps için, arama kutusuna filtreniz olarak "FTP" yazın. Tetikleyiciler listesinde istediğiniz tetikleyiciyi seçin.

   -veya-

   Mevcut Logic Apps için, eylem eklemek istediğiniz son adım altında **yeni adım**' ı seçin ve ardından **Eylem Ekle**' yi seçin. Arama kutusuna filtreniz olarak "FTP" yazın. Eylemler listesi altında istediğiniz eylemi seçin.

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve **Eylem Ekle**' yi seçin.

1. Bağlantınız için gerekli ayrıntıları sağlayın ve **Oluştur**' u seçin.

1. Seçtiğiniz tetikleyici veya eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="examples"></a>Örnekler

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>FTP tetikleyicisi: Bir dosya eklendiğinde veya değiştirildiğinde

Bu tetikleyici, bir FTP sunucusunda bir dosya eklendiğinde veya değiştirildiğinde tetikleyici algıladığında bir mantıksal uygulama iş akışı başlatır. Örneğin, dosyanın içeriğini denetleyen bir koşul ekleyebilir ve bu içeriğin belirli bir koşulu karşılayıp karşılamadığını temel alarak bu içeriği almak isteyip istemediğinizi karar verebilirsiniz. Son olarak, dosyanın içeriğini alan ve bu içeriği SFTP sunucusundaki bir klasöre koyabileceğiniz bir eylem ekleyebilirsiniz.

**Kurumsal örnek**: Bu tetikleyiciyi, müşteri siparişlerini tanımlayan yeni dosyalar için FTP klasörünü izlemek üzere kullanabilirsiniz. Daha sonra **Dosya Içeriğini al**gıbı bir FTP eylemi kullanabilirsiniz, böylece siparişin içeriğini daha fazla işleme için alabilir ve bu siparişi bir Siparişler veritabanında depoaktarabilirsiniz.

Bu tetikleyiciyi gösteren bir örnek aşağıda verilmiştir: **Bir dosya eklendiğinde veya değiştirildiğinde**

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Boş Logic Apps için, arama kutusuna filtreniz olarak "FTP" yazın. Tetikleyiciler listesinde, bu tetikleyiciyi seçin: **Bir dosyalanmış eklendiğinde veya değiştirildiğinde-FTP**

   ![FTP tetikleyicisini bul ve Seç](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Bağlantınız için gerekli ayrıntıları sağlayın ve **Oluştur**' u seçin.

   Varsayılan olarak, bu bağlayıcı dosyaları metin biçiminde aktarır. Dosyaları ikili biçimde (örneğin, kodlama kullanıldığı yerde) aktarmak için **Ikili taşıma**' yı seçin.

   ![FTP sunucusu bağlantısı oluştur](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. **Klasör** kutusu ' nun yanında, bir liste görünecek şekilde klasör simgesini seçin. Yeni veya düzenlenmiş dosyalar için izlemek istediğiniz klasörü bulmak için, doğru açılı oku ( **>** ) seçin, bu klasöre gidin ve sonra klasörü seçin.

   ![İzlenecek klasörü bul ve Seç](./media/connectors-create-api-ftp/select-folder.png)  

   Seçtiğiniz klasör **klasör** kutusunda görünür.

   ![Seçili klasör](./media/connectors-create-api-ftp/selected-folder.png)  

Artık mantıksal uygulamanızın bir tetikleyicisi olduğuna göre, mantıksal uygulamanız yeni veya düzenlenmiş bir dosya bulduğunda çalıştırmak istediğiniz eylemleri ekleyin. Bu örnekte, yeni veya güncelleştirilmiş içeriği alan bir FTP eylemi ekleyebilirsiniz.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>FTP eylemi: İçerik al

Bu eylem, bu dosya eklendiğinde veya güncelleştirilirken FTP sunucusundaki bir dosyanın içeriğini alır. Örneğin, bir önceki örnekteki tetikleyiciyi ve dosya eklendikten veya düzenlendikten sonra dosyanın içeriğini alan bir eylemden ekleme yapabilirsiniz.

Bu eylemi gösteren bir örnek aşağıda verilmiştir: **İçerik al**

1. Tetikleyici veya başka herhangi bir eylem altında **yeni adım**' ı seçin.

1. Arama kutusuna filtreniz olarak "FTP" yazın. Eylemler listesi altında şu eylemi seçin: **Dosya içeriğini al-FTP**

   ![FTP eylemini seçin](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Zaten bir FTP sunucunuz ve hesabınızla bağlantınız varsa, sonraki adıma geçin. Aksi takdirde, bu bağlantı için gerekli ayrıntıları sağlayın ve **Oluştur**' u seçin.

   ![FTP sunucusu bağlantısı oluştur](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. **Dosya Içeriğini al** eylemi açıldıktan sonra, dinamik içerik listesinin görünmesi için **Dosya** kutusunun içine tıklayın. Artık önceki adımlardan çıktılar için özellikler seçebilirsiniz. Dinamik içerik listesinden, eklenen veya güncellenen dosyanın içeriğine sahip olan **dosya içeriği** özelliğini seçin.  

   ![Dosya bul ve Seç](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   **Dosya içeriği** özelliği artık **Dosya** kutusunda görünür.

   ![Seçili "dosya Içeriği" özelliği](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Mantıksal uygulamanızı kaydedin. İş akışınızı test etmek için, mantıksal uygulamanızın artık izlediği FTP klasörüne bir dosya ekleyin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için [bağlayıcının başvuru sayfasını](/connectors/ftpconnector/)gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
