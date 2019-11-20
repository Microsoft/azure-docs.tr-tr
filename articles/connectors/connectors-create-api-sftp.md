---
title: SFTP hesabına bağlanma-Azure Logic Apps
description: Azure Logic Apps kullanarak bir SFTP sunucusu için dosyaları izleyen, oluşturan, yöneten, gönderen ve alan görevleri ve işlemleri otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: 20702f5bc0dfc513d1fba84b69595dec885613ac
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837104"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak SFTP dosyalarını izleme, oluşturma ve yönetme

> [!IMPORTANT]
> SFTP Bağlayıcısı kullanım dışı olduğundan lütfen [SFTP-SSH bağlayıcısını](../connectors/connectors-sftp-ssh.md) kullanın. Mantıksal uygulama Tasarımcısı 'nda artık SFTP Tetikleyicileri ve eylemleri seçemezsiniz.

[Güvenli bir Dosya Aktarım Protokolü (SFTP)](https://www.ssh.com/ssh/sftp/) sunucusunda dosya izleyen, oluşturan, gönderen ve alan görevleri otomatikleştirmek için, Azure Logic Apps ve SFTP bağlayıcısını kullanarak tümleştirme iş akışları oluşturabilir ve otomatikleştirebilirsiniz. SFTP, herhangi bir güvenilir veri akışı üzerinde dosya erişimi, dosya aktarımı ve dosya yönetimi sağlayan bir ağ protokolüdür. Otomatikleştirebileceğiniz bazı örnek görevler şunlardır:

* Dosya eklendiğinde veya değiştirildiğinde izleyin.
* Dosyaları alın, oluşturun, kopyalayın, güncelleştirin, listeleyin ve silin.
* Dosya içeriğini ve meta verileri alın.
* Arşivi klasörlere ayıklayın.

SFTP sunucunuzdaki olayları izleyen ve çıktıyı diğer eylemler için kullanılabilir hale getirmek için Tetikleyicileri kullanabilirsiniz. SFTP sunucunuzda çeşitli görevleri gerçekleştiren işlemleri kullanabilirsiniz. Ayrıca, mantıksal uygulamanızdaki diğer eylemlere SFTP eylemlerinden gelen çıktıyı kullanın. Örneğin, SFTP sunucusundan düzenli olarak dosya alırsanız, Office 365 Outlook bağlayıcısını veya Outlook.com bağlayıcısını kullanarak bu dosyalar ve bunların içerikleri hakkında e-posta uyarıları gönderebilirsiniz. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

## <a name="limits"></a>Sınırlar

SFTP Bağlayıcısı yalnızca *50 MB veya daha küçük* olan dosyaları işler ve [ileti parçalama](../logic-apps/logic-apps-handle-large-messages.md)'yi desteklemez. Daha büyük dosyalar için [SFTP-SSH bağlayıcısını](../connectors/connectors-sftp-ssh.md)kullanın. SFTP Bağlayıcısı ve SFTP-SSH Bağlayıcısı arasındaki farklar için SFTP-SSH makalesindeki SFTP [-SSH Ile SFTP karşılaştırması](../connectors/connectors-sftp-ssh.md#comparison) konusunu gözden geçirin.

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* SFTP sunucunuzun adresi ve hesap kimlik bilgileriniz, mantıksal uygulamanızın SFTP hesabınıza erişmesine izin verir. [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokolünü kullanmak için SSH özel ANAHTARıNA ve SSH özel anahtar parolasına erişmeniz de gerekir.

  > [!NOTE]
  >
  > SFTP Bağlayıcısı şu özel anahtar biçimlerini destekler: OpenSSH, ssh.com ve PuTTY
  >
  > Mantıksal uygulamanızı oluştururken, istediğiniz SFTP tetikleyicisini veya eylemini ekledikten sonra SFTP sunucunuz için bağlantı bilgilerini sağlamanız gerekir. 
  > SSH özel anahtarı kullanıyorsanız, SSH özel anahtar dosyanızdaki anahtarı ***kopyalamadığınızdan*** emin olun ve bu anahtarı bağlantı ayrıntılarına ***yapıştırın*** , ***anahtarı el ile girmeyin veya düzenlemeyin***, bu da bağlantının başarısız olmasına neden olabilir. 
  > Daha fazla bilgi için bu makaledeki sonraki adımlara bakın.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* SFTP hesabınıza erişmek istediğiniz mantıksal uygulama. Bir SFTP tetikleyicisiyle başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Bir SFTP eylemi kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="how-sftp-triggers-work"></a>SFTP nasıl çalışır?

SFTP Tetikleyicileri, SFTP dosya sistemini yoklayarak ve Son yoklamadan bu yana değiştirilen herhangi bir dosyayı arayarak çalışır. Bazı araçlar, dosyalar değiştiğinde zaman damgasını korumanıza olanak sağlar. Bu durumlarda, tetikleyicinizin çalışabilmesi için bu özelliği devre dışı bırakmanız gerekir. Yaygın olarak kullanılan bazı ayarlar şunlardır:

| SFTP istemcisi | Eylem |
|-------------|--------|
| WinSCP | **Seçenekler** > **tercihleri** ' ne gidin > **Aktar** > **Düzenle** > **zaman damgasını koru** > **devre dışı bırak** |
| FileZilla | **Aktarım** > git > **aktarılan dosyaların zaman damgalarını koru ve** **devre dışı bırak** |
|||

Tetikleyici yeni bir dosya bulduğunda, tetikleyici yeni dosyanın tamamlandığını ve kısmen yazılmadığını denetler. Örneğin, tetikleyici dosya sunucusunu denetlerken bir dosya sürmekte olan değişiklikler olabilir. Kısmen yazılmış bir dosyanın döndürülmemek için tetikleyici, son değişiklikleri olan dosyanın zaman damgasını Not etmez, ancak bu dosyayı hemen döndürmez. Tetikleyici dosyayı yalnızca sunucuyu yoklayarak geri döndürür. Bazen bu davranış, tetikleyicinin yoklama aralığı iki katına varan bir gecikmeye neden olabilir.

## <a name="connect-to-sftp"></a>SFTP 'ye bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Boş Logic Apps için, arama kutusuna filtreniz olarak "SFTP" yazın. Tetikleyiciler listesinde istediğiniz tetikleyiciyi seçin.

   -veya-

   Mevcut Logic Apps için, eylem eklemek istediğiniz son adım altında **yeni adım**' ı seçin. Arama kutusuna filtreniz olarak "SFTP" yazın. Eylemler listesi altında istediğiniz eylemi seçin.

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Bağlantınız için gerekli ayrıntıları sağlayın.

   > [!IMPORTANT]
   >
   > **SSH özel anahtar ÖZELLIĞINDE SSH** özel anahtarınızı girdiğinizde, bu özellik için tüm ve doğru değeri sağladığınızdan emin olmanıza yardımcı olan bu ek adımları izleyin. 
   > Geçersiz bir anahtar bağlantının başarısız olmasına neden olur.

   Herhangi bir metin düzenleyicisini kullanabilseniz de, örnek olarak Notepad. exe ' yi kullanarak anahtarınızın doğru şekilde kopyalanıp yapıştırılacağını gösteren örnek adımlar aşağıda verilmiştir.

   1. SSH özel anahtar dosyanızı bir metin düzenleyicisinde açın. Bu adımlar örnek olarak not defteri 'Ni kullanır.

   1. Not defteri **düzenleme** menüsünde **Tümünü Seç**' i seçin.

   1.  > **kopyasını** **Düzenle** ' yi seçin.

   1. Eklediğiniz SFTP tetikleyicisi veya eyleminde, birden çok satırı destekleyen **SSH özel anahtar** özelliğine kopyaladığınız *bütün* anahtarı yapıştırın. Anahtarı ***yapıştırdığınızdan emin olun*** . ***Anahtarı el ile girmeyin veya düzenleyemezsiniz***.

1. Bağlantı ayrıntılarını girmeyi tamamladığınızda **Oluştur**' u seçin.

1. Seçtiğiniz tetikleyici veya eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="examples"></a>Örnekler

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP tetikleyicisi: bir dosya eklendiğinde veya değiştirildiğinde

Bu tetikleyici bir SFTP sunucusunda dosya eklendiğinde veya değiştirildiğinde bir mantıksal uygulama iş akışı başlatır. Örneğin, dosyanın içeriğini denetleyen ve içeriğin belirtilen bir koşulu karşılayıp karşılamadığını temel alarak içeriği alan bir koşul ekleyebilirsiniz. Daha sonra dosyanın içeriğini alan ve bu içeriği SFTP sunucusundaki bir klasöre yerleştiren bir eylem ekleyebilirsiniz.

**Kurumsal örnek**: Bu tetikleyiciyi, Müşteri emirlerini temsil eden yeni dosyalar IÇIN BIR SFTP klasörünü izlemek üzere kullanabilirsiniz. Daha sonra, daha fazla işleme için siparişin içeriğini almak ve bu siparişi bir Siparişler veritabanında depolamak için **Dosya Içeriğini al** gıbı bır SFTP eylemi kullanabilirsiniz.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP eylemi: içerik al

Bu eylem, SFTP sunucusundaki bir dosyanın içeriğini alır. Örneğin, önceki örnekteki tetikleyiciyi ve dosyanın içeriğinin uyması gereken bir koşulu ekleyebilirsiniz. Koşul doğru ise, içeriği alan eylem çalıştırılabilir.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/sftpconnector/)gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
