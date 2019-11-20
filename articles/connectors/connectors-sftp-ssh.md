---
title: SFTP sunucusuna SSH ile bağlanma-Azure Logic Apps
description: SSH ve Azure Logic Apps kullanarak bir SFTP sunucusu için dosya izleme, oluşturma, yönetme, gönderme ve alma görevlerini otomatikleştirin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: f52fc91d218e1a5448f6e6e7465f6416a04fd67d
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837145"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>SSH ve Azure Logic Apps kullanarak SFTP dosyalarını izleme, oluşturma ve yönetme

[Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokolünü kullanarak [güvenli bir Dosya Aktarım Protokolü (SFTP)](https://www.ssh.com/ssh/sftp/) sunucusunda dosyaları izleyen, oluşturan, gönderen ve alan görevleri otomatik hale getirmek için Azure Logic Apps ve SFTP-SSH kullanarak tümleştirme iş akışları oluşturabilir ve otomatikleştirebilirsiniz Bağlayıcısı. SFTP, herhangi bir güvenilir veri akışı üzerinde dosya erişimi, dosya aktarımı ve dosya yönetimi sağlayan bir ağ protokolüdür. Otomatikleştirebileceğiniz bazı örnek görevler şunlardır:

* Dosya eklendiğinde veya değiştirildiğinde izleyin.
* Dosyaları alın, oluşturun, kopyalayın, yeniden adlandırın, güncelleştirin, listeleyin ve silin.
* Klasörler oluşturun.
* Dosya içeriğini ve meta verileri alın.
* Arşivi klasörlere ayıklayın.

SFTP sunucunuzdaki olayları izleyen ve çıktıyı diğer eylemler için kullanılabilir hale getirmek için Tetikleyicileri kullanabilirsiniz. SFTP sunucunuzda çeşitli görevleri gerçekleştiren işlemleri kullanabilirsiniz. Ayrıca, mantıksal uygulamanızdaki diğer eylemlere SFTP eylemlerinden gelen çıktıyı kullanın. Örneğin, SFTP sunucusundan düzenli olarak dosya alırsanız, Office 365 Outlook bağlayıcısını veya Outlook.com bağlayıcısını kullanarak bu dosyalar ve bunların içerikleri hakkında e-posta uyarıları gönderebilirsiniz. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

SFTP-SSH Bağlayıcısı ve SFTP Bağlayıcısı arasındaki farklar için, bu konunun ilerleyen kısımlarında [SFTP-SSH Ile SFTP karşılaştırması](#comparison) bölümüne bakın.

## <a name="limits"></a>Sınırlar

* Varsayılan olarak, SFTP-SSH eylemleri, *1 GB veya daha küçük* olan, ancak aynı anda yalnızca *15 MB* öbekteki dosyaları okuyabilir veya yazabilir. 15 MB 'tan büyük dosyaları işlemek için SFTP-SSH eylemleri, yalnızca 15 MB 'lık dosyaları işleyebilen Dosya Kopyala eylemi dışında [ileti parçalama](../logic-apps/logic-apps-handle-large-messages.md)desteği sağlar. **Dosya Içeriğini al** eylemi örtük olarak ileti parçalama kullanır.

* SFTP-SSH Tetikleyicileri parçalama desteklemez. Dosya içeriği istenirken Tetikleyiciler yalnızca 15 MB veya daha küçük olan dosyaları seçer. 15 MB 'tan büyük dosyaları almak için, bunun yerine şu modele uyun:

  * Dosya **ekleme veya değiştirme (yalnızca Özellikler)** gibi dosya özelliklerini döndüren bir SFTP-SSH tetikleyicisi kullanın.

  * Tam dosyayı okuyan ve örtük olarak ileti parçalama kullanan SFTP-SSH **Dosya Içeriğini al** eylemiyle tetikleyiciyi izleyin.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH ile SFTP karşılaştırması

SFTP-SSH Bağlayıcısı ile SFTP-SSH bağlayıcısının bu yeteneklere sahip olduğu SFTP Bağlayıcısı arasındaki diğer önemli farklılıklar aşağıda verilmiştir:

* , .NET 'i destekleyen bir açık kaynaklı Secure Shell (SSH) kitaplığı olan [SSH.net kitaplığını](https://github.com/sshnet/SSH.NET)kullanır.

* Varsayılan olarak, SFTP-SSH eylemleri, *1 GB veya daha küçük* olan, ancak aynı anda yalnızca *15 MB* öbekteki dosyaları okuyabilir veya yazabilir.

  15 MB 'tan büyük dosyaları işlemek için SFTP-SSH eylemleri [ileti parçalama](../logic-apps/logic-apps-handle-large-messages.md)kullanabilir. Ancak, bu eylem ileti parçalama işlemini desteklemediğinden, Dosya Kopyala eylemi yalnızca 15 MB dosyayı destekler. SFTP-SSH Tetikleyicileri parçalama desteklemez. Büyük dosyaları karşıya yüklemek için SFTP sunucunuzdaki kök klasör için hem okuma hem de yazma izinlerine sahip olmanız gerekir.

* SFTP sunucusundaki belirtilen yolda bir klasör oluşturan **klasör oluştur** eylemini sağlar.

* SFTP sunucusundaki bir dosyayı **yeniden adlandıran dosyayı yeniden adlandır** eylemini sağlar.

* SFTP sunucusu bağlantısını, performansı artıran ve sunucuya bağlanma girişimlerinin sayısını azaltan *1 saate kadar*önbelleğe alır. Bu önbelleğe alma davranışının süresini ayarlamak için SFTP sunucunuzdaki SSH yapılandırmasındaki [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) özelliğini düzenleyin.

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* SFTP sunucunuzun adresi ve hesap kimlik bilgileriniz, mantıksal uygulamanızın SFTP hesabınıza erişmesine izin verir. Ayrıca SSH özel anahtarına ve SSH özel anahtar parolasına erişmeniz gerekir. Büyük dosyaları karşıya yüklerken parçalama kullanmak için SFTP sunucunuzdaki kök klasör için hem okuma hem de yazma izinlerine sahip olmanız gerekir. Aksi takdirde, bir "401 Yetkisiz" hatası alırsınız.

  > [!IMPORTANT]
  >
  > SFTP-SSH Bağlayıcısı *yalnızca* şu özel anahtar biçimlerini, algoritmaları ve parmak izlerini destekler:
  >
  > * **Özel anahtar formatları**: RSA (Rivest Shamir Adtaman) ve dsa (dijital imza algoritması) anahtarlar hem OpenSSH hem de SSH.com biçimlerinde. Özel anahtarınız PuTTY (. PPK) dosya biçimindeyse, önce [anahtarı OpenSSH (. pem) dosya biçimine dönüştürmeniz](#convert-to-openssh)gerekir.
  >
  > * **Şifreleme algoritmaları**: des-EDE3-CBC, des-EDE3-CFB, des-CBC, aes-128-CBC, aes-192-CBC ve AES-256-CBC
  >
  > * **Parmak izi**: MD5
  >
  > Mantıksal uygulamanıza istediğiniz SFTP-SSH tetikleyicisini veya eylemini ekledikten sonra, SFTP sunucunuz için bağlantı bilgilerini sağlamanız gerekir. Bu bağlantı için SSH özel anahtarınızı sağladığınızda ***anahtarı el ile girmeyin veya düzenlemeyin***, bu da bağlantının başarısız olmasına neden olabilir. Bunun yerine, anahtarı SSH özel anahtar dosyanızdaki ***kopyalamayın*** ve bu anahtarı bağlantı ayrıntılarına ***yapıştırdığınızdan*** emin olun. 
  > Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [SFTP 'ye SSH Ile bağlanma](#connect) bölümüne bakın.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* SFTP hesabınıza erişmek istediğiniz mantıksal uygulama. Bir SFTP-SSH tetikleyicisiyle başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Bir SFTP-SSH eylemi kullanmak için, mantıksal uygulamanızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="how-sftp-ssh-triggers-work"></a>SFTP-SSH Tetikleyicileri nasıl çalışır?

SFTP-SSH Tetikleyicileri SFTP dosya sistemini yoklayarak ve Son yoklamadan bu yana değiştirilen herhangi bir dosyayı arayarak çalışır. Bazı araçlar, dosyalar değiştiğinde zaman damgasını korumanıza olanak sağlar. Bu durumlarda, tetikleyicinizin çalışabilmesi için bu özelliği devre dışı bırakmanız gerekir. Yaygın olarak kullanılan bazı ayarlar şunlardır:

| SFTP istemcisi | Eylem |
|-------------|--------|
| WinSCP | **Seçenekler** > **tercihleri** ' ne gidin > **Aktar** > **Düzenle** > **zaman damgasını koru** > **devre dışı bırak** |
| FileZilla | **Aktarım** > git > **aktarılan dosyaların zaman damgalarını koru ve** **devre dışı bırak** |
|||

Tetikleyici yeni bir dosya bulduğunda, tetikleyici yeni dosyanın tamamlandığını ve kısmen yazılmadığını denetler. Örneğin, tetikleyici dosya sunucusunu denetlerken bir dosya sürmekte olan değişiklikler olabilir. Kısmen yazılmış bir dosyanın döndürülmemek için tetikleyici, son değişiklikleri olan dosyanın zaman damgasını Not etmez, ancak bu dosyayı hemen döndürmez. Tetikleyici dosyayı yalnızca sunucuyu yoklayarak geri döndürür. Bazen bu davranış, tetikleyicinin yoklama aralığı iki katına varan bir gecikmeye neden olabilir.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>PuTTY tabanlı anahtarı OpenSSH 'ye Dönüştür

Özel anahtarınız,. PPK (PuTTY özel anahtarı) dosya adı uzantısını kullanan PuTTY biçimindeyse, önce anahtarı. pem (Gizlilik Gelişmiş posta) dosya adı uzantısını kullanan OpenSSH biçimine dönüştürün.

### <a name="unix-based-os"></a>UNIX tabanlı işletim sistemi

1. PuTTY araçları sisteminizde zaten yüklü değilse, bunu şimdi yapın, örneğin:

   `sudo apt-get install -y putty`

1. SFTP-SSH Bağlayıcısı ile kullanabileceğiniz bir dosya oluşturan bu komutu çalıştırın:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Örneğin:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows İşletim Sistemi

1. Daha önce yapmadıysanız, [en son PuTTY Oluşturucu (PuTTYgen. exe) aracını indirin](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)ve ardından aracı başlatın.

1. Bu ekranda **Yükle**' yi seçin.

   !["Yükle" yi seçin](./media/connectors-sftp-ssh/puttygen-load.png)

1. PuTTY biçimindeki özel anahtar dosyanıza gidin ve **Aç**' ı seçin.

1. **Dönüşümler** menüsünde, **OpenSSH anahtarını dışarı aktar**' ı seçin.

   !["OpenSSH anahtarını dışarı aktar" ı seçin](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Özel anahtar dosyasını `.pem` dosya adı uzantısıyla kaydedin.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>SSH ile SFTP 'ye bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Boş Logic Apps için, arama kutusuna filtreniz olarak "SFTP SSH" yazın. Tetikleyiciler listesinde istediğiniz tetikleyiciyi seçin.

   -veya-

   Mevcut Logic Apps için, eylem eklemek istediğiniz son adım altında **yeni adım**' ı seçin. Arama kutusuna filtreniz olarak "SFTP SSH" yazın. Eylemler listesi altında istediğiniz eylemi seçin.

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Bağlantınız için gerekli ayrıntıları sağlayın.

   > [!IMPORTANT]
   >
   > **SSH özel anahtar ÖZELLIĞINDE SSH** özel anahtarınızı girdiğinizde, bu özellik için tüm ve doğru değeri sağladığınızdan emin olmanıza yardımcı olan bu ek adımları izleyin. Geçersiz bir anahtar bağlantının başarısız olmasına neden olur.

   Herhangi bir metin düzenleyicisini kullanabilseniz de, örnek olarak Notepad. exe ' yi kullanarak anahtarınızın doğru şekilde kopyalanıp yapıştırılacağını gösteren örnek adımlar aşağıda verilmiştir.

   1. SSH özel anahtar dosyanızı bir metin düzenleyicisinde açın. Bu adımlar örnek olarak not defteri 'Ni kullanır.

   1. Not defteri **düzenleme** menüsünde **Tümünü Seç**' i seçin.

   1.  > **kopyasını** **Düzenle** ' yi seçin.

   1. Eklediğiniz SFTP-SSH tetikleyicisi veya eyleminde, birden çok satırı destekleyen **SSH özel anahtar** özelliğine kopyaladığınız *tam* anahtarı yapıştırın.  Anahtarı ***yapıştırdığınızdan emin olun*** . ***Anahtarı el ile girmeyin veya düzenleyemezsiniz***.

1. Bağlantı ayrıntılarını girmeyi tamamladığınızda **Oluştur**' u seçin.

1. Şimdi seçtiğiniz tetikleyici veya eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="examples"></a>Örnekler

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP-SSH tetikleyicisi: bir dosya eklendiğinde veya değiştirildiğinde

Bu tetikleyici bir SFTP sunucusunda dosya eklendiğinde veya değiştirildiğinde bir mantıksal uygulama iş akışı başlatır. Örneğin, dosyanın içeriğini denetleyen ve içeriğin belirtilen bir koşulu karşılayıp karşılamadığını temel alarak içeriği alan bir koşul ekleyebilirsiniz. Daha sonra dosyanın içeriğini alan ve bu içeriği SFTP sunucusundaki bir klasöre yerleştiren bir eylem ekleyebilirsiniz.

**Kurumsal örnek**: Bu tetikleyiciyi, Müşteri emirlerini temsil eden yeni dosyalar IÇIN BIR SFTP klasörünü izlemek üzere kullanabilirsiniz. Daha sonra, daha fazla işleme için siparişin içeriğini almak ve bu siparişi bir Siparişler veritabanında depolamak için **Dosya Içeriğini al** gıbı bır SFTP eylemi kullanabilirsiniz.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP-SSH eylemi: yolu kullanarak içerik al

Bu eylem, SFTP sunucusundaki bir dosyanın içeriğini alır. Örneğin, önceki örnekteki tetikleyiciyi ve dosyanın içeriğinin uyması gereken bir koşulu ekleyebilirsiniz. Koşul doğru ise, içeriği alan eylem çalıştırılabilir.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](/connectors/sftpconnector/)gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
