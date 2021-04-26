---
title: SSH ile SFTP sunucusuna bağlanma
description: SSH ve Azure Logic Apps kullanarak bir SFTP sunucusu için dosya izleme, oluşturma, yönetme, gönderme ve alma görevlerini otomatikleştirin
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/19/2021
tags: connectors
ms.openlocfilehash: a19253e117f748b4d4045bfd2a29552018bba91e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781568"
---
# <a name="create-and-manage-sftp-files-using-ssh-and-azure-logic-apps"></a>SSH ve Azure Logic Apps kullanarak SFTP dosyaları oluşturma ve yönetme

[Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokolünü kullanarak [güvenli bir Dosya Aktarım Protokolü (SFTP)](https://www.ssh.com/ssh/sftp/) sunucusunda dosya oluşturup yöneten görevleri otomatik hale getirmek için Azure Logic Apps ve SFTP-SSH bağlayıcısını kullanarak otomatik tümleştirme iş akışları oluşturabilirsiniz. SFTP, herhangi bir güvenilir veri akışı üzerinde dosya erişimi, dosya aktarımı ve dosya yönetimi sağlayan bir ağ protokolüdür.

Otomatikleştirebileceğiniz bazı örnek görevler şunlardır:

* Dosya eklendiğinde veya değiştirildiğinde izleyin.
* Dosyaları alın, oluşturun, kopyalayın, yeniden adlandırın, güncelleştirin, listeleyin ve silin.
* Klasörler oluşturun.
* Dosya içeriğini ve meta verileri alın.
* Arşivi klasörlere ayıklayın.

İş akışınızda, SFTP sunucunuzdaki olayları izleyen bir tetikleyici kullanabilir ve çıktıyı diğer eylemler için kullanılabilir hale getirebilirsiniz. Ardından, SFTP sunucunuzda çeşitli görevleri gerçekleştirmek için Eylemler kullanabilirsiniz. Ayrıca, SFTP-SSH eylemleriyle ilgili çıktıyı kullanan diğer eylemleri de dahil edebilirsiniz. Örneğin, SFTP sunucusundan düzenli olarak dosya alırsanız, Office 365 Outlook bağlayıcısını veya Outlook.com bağlayıcısını kullanarak bu dosyalar ve içerikleri hakkında e-posta uyarıları gönderebilirsiniz. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

SFTP-SSH Bağlayıcısı ve SFTP Bağlayıcısı arasındaki farklar için, bu konunun ilerleyen kısımlarında [SFTP-SSH Ile SFTP karşılaştırması](#comparison) bölümüne bakın.

## <a name="limits"></a>Sınırlar

* SFTP-SSH Bağlayıcısı Şu anda bu SFTP sunucularını desteklememektedir:

  * IBM veri gücü
  * MessageWay
  * OpenText güvenli MFT
  * OpenText GXS

* SFTP- [öbek](../logic-apps/logic-apps-handle-large-messages.md) oluşturma 'Yı destekleyen SSH EYLEMLERI 1 GB 'a kadar dosya işleyebilir, ancak öbek desteklemeyen SFTP-ssh EYLEMLERI 50 MB 'a kadar olan dosyaları işleyebilir. Varsayılan öbek boyutu 15 MB 'tır. Ancak, bu boyut, 5 MB 'den başlayarak dinamik olarak değişebilir ve 50 MB 'lık en yüksek düzeyde artarak arttırılarak. Dinamik boyutlandırma, ağ gecikmesi, sunucu yanıt süresi vb. gibi faktörlere göre belirlenir.

  > [!NOTE]
  > Bir [tümleştirme hizmeti ortamındaki (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Logic Apps için, bu bağlayıcının Ise etiketli sürümü, bunun yerine [Ise ileti sınırlarını](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) kullanmasını gerektirir.

  Bunun yerine kullanılacak [sabit bir öbek boyutu belirttiğinizde](#change-chunk-size) , bu Uyarlamalı davranışı geçersiz kılabilirsiniz. Bu boyut 5 MB ile 50 MB arasında değişebilir. Örneğin, bir 45 MB dosyanız olduğunu ve bu dosya boyutunu gecikme süresi olmadan destekleyebildiğini belirten bir ağ olduğunu varsayalım. Uyarlamalı öbek oluşturma, bir çağrı yerine birkaç çağrıya neden olur. Çağrı sayısını azaltmak için 50 MB 'lık bir öbek boyutunu ayarlamayı deneyebilirsiniz. Farklı bir senaryoda, mantıksal uygulamanız zaman aşımına uğradıysanız (örneğin, 15 MB öbekleri kullanırken) boyutu 5 MB olarak azaltmayı deneyebilirsiniz.

  Öbek boyutu bir bağlantıyla ilişkili. Bu öznitelik, her iki eylem için de parçalama desteği olmayan eylemler için aynı bağlantıyı kullanabileceğiniz anlamına gelir. Bu durumda, öbek oluşturma desteği olmayan eylemler için öbek boyutu 5 MB ile 50 MB arasındadır. Bu tabloda, hangi SFTP-SSH eylemlerinin öbek oluşturma desteği gösterilmektedir:

  | Eylem | Öbek oluşturma desteği | Öbek boyutu desteğini geçersiz kıl |
  |--------|------------------|-----------------------------|
  | **Dosyayı Kopyala** | Hayır | Geçerli değil |
  | **Dosya oluştur** | Yes | Yes |
  | **Klasör oluştur** | Uygulanamaz | Uygulanamaz |
  | **Dosyayı Sil** | Uygulanamaz | Uygulanamaz |
  | **Arşivi klasöre Ayıkla** | Uygulanamaz | Uygulanamaz |
  | **Dosya içeriğini al** | Yes | Yes |
  | **Yolu kullanarak dosya içeriğini al** | Yes | Yes |
  | **Dosya meta verilerini al** | Uygulanamaz | Uygulanamaz |
  | **Yolu kullanarak dosya meta verilerini al** | Uygulanamaz | Uygulanamaz |
  | **Klasördeki dosyaları Listele** | Uygulanamaz | Uygulanamaz |
  | **Dosyayı yeniden adlandır** | Uygulanamaz | Uygulanamaz |
  | **Güncelleştirme dosyası** | Hayır | Geçerli değil |
  ||||

* SFTP-SSH Tetikleyicileri ileti parçalama desteği vermez. Dosya içeriği istenirken Tetikleyiciler yalnızca 15 MB veya daha küçük olan dosyaları seçer. 15 MB 'tan büyük dosyaları almak için, bunun yerine şu modele uyun:

  1. Yalnızca dosya özelliklerini döndüren bir SFTP-SSH tetikleyicisi kullanın. Bu Tetikleyiciler, açıklamayı içeren adlara sahiptir **(yalnızca Özellikler)**.

  1. SFTP-SSH **Dosya Içeriğini al** eylemiyle tetikleyiciyi izleyin. Bu eylem, tam dosyayı okur ve örtülü olarak ileti parçalama kullanır.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH ile SFTP karşılaştırması

Aşağıdaki listede, SFTP bağlayıcısından farklı olan önemli SFTP-SSH özellikleri açıklanmaktadır:

* , .NET 'i destekleyen bir açık kaynaklı Secure Shell (SSH) kitaplığı olan [SSH.net kitaplığını](https://github.com/sshnet/SSH.NET)kullanır.

* SFTP sunucusundaki belirtilen yolda bir klasör oluşturan **klasör oluştur** eylemini sağlar.

* SFTP sunucusundaki bir dosyayı **yeniden adlandıran dosyayı yeniden adlandır** eylemini sağlar.

* SFTP sunucusu bağlantısını *1 saate kadar* önbelleğe alır. Bu özellik, performansı artırır ve bağlayıcının sunucuya bağlanmayı kaç kez deneyi azaltır. Bu önbelleğe alma davranışının süresini ayarlamak için SFTP sunucunuzdaki SSH yapılandırmasındaki [ **ClientAliveInterval** özelliğini](https://man.openbsd.org/sshd_config#ClientAliveInterval) düzenleyin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* SFTP sunucunuzun adresi ve hesap kimlik bilgileriniz, bu nedenle iş akışınız SFTP hesabınıza erişebilir. Ayrıca SSH özel anahtarına ve SSH özel anahtar parolasına erişmeniz gerekir. Öbek kullanarak büyük dosyaları karşıya yüklemek için SFTP sunucunuzdaki kök klasör için hem okuma hem de yazma erişimine sahip olmanız gerekir. Aksi takdirde, bir "401 Yetkisiz" hatası alırsınız.

  SFTP-SSH Bağlayıcısı hem özel anahtar kimlik doğrulaması hem de parola kimlik doğrulamasını destekler. Ancak SFTP-SSH Bağlayıcısı *yalnızca* bu özel anahtar biçimlerini, algoritmaları ve parmak izlerini destekler:

  * **Özel anahtar formatları**: RSA (Rivest Shamir Adtaman) ve dsa (dijital imza algoritması) anahtarlar hem OpenSSH hem de SSH.com biçimlerinde. Özel anahtarınız PuTTY (. PPK) dosya biçimindeyse, önce [anahtarı OpenSSH (. pem) dosya biçimine dönüştürmeniz](#convert-to-openssh)gerekir.
  * **Şifreleme algoritmaları**: des-EDE3-CBC, des-EDE3-CFB, des-CBC, aes-128-CBC, aes-192-CBC ve AES-256-CBC
  * **Parmak izi**: MD5

  İş akışınıza bir SFTP-SSH tetikleyicisi veya eylemi ekledikten sonra, SFTP sunucunuz için bağlantı bilgilerini sağlamanız gerekir. Bu bağlantı için SSH özel anahtarınızı sağladığınızda ***anahtarını el ile girmeyin veya düzenleyemezsiniz**, bu da bağlantının başarısız olmasına neden olabilir. Bunun yerine, anahtarı SSH özel anahtar dosyasından _*_kopyalamadığınızdan_*_ emin olun ve _ ' i * Bu anahtardan bağlantı ayrıntılarına *_yapıştırın_*. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [SFTP 'ye SSH Ile bağlanma](#connect) bölümüne bakın.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* SFTP hesabınıza erişmek istediğiniz mantıksal uygulama iş akışı. Bir SFTP-SSH tetikleyicisiyle başlamak için [boş bir mantıksal uygulama iş akışı oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). Bir SFTP-SSH eylemi kullanmak için, iş akışınızı başka bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="how-sftp-ssh-triggers-work"></a>SFTP-SSH tetikleyicileri nasıl çalışır?

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>Yoklama davranışı

SFTP-SSH Tetikleyicileri SFTP dosya sistemini yoklayın ve Son yoklamadan bu yana değiştirilen tüm dosyaları arar. Bazı araçlar, dosyalar değiştiğinde zaman damgasını korumanıza olanak sağlar. Bu durumlarda, tetikleyicinizin çalışabilmesi için bu özelliği devre dışı bırakmanız gerekir. Yaygın olarak kullanılan bazı ayarlar şunlardır:

| SFTP istemcisi | Eylem |
|-------------|--------|
| WinSCP | **Seçenekler**  >  **Tercihler**  >  **Aktarım**  >  **düzenleme**  >  **zaman damgası**  >  **devre dışı bırak** ' a gidin |
| FileZilla | **Aktarım**' a git  >  **aktarılan dosyaların zaman damgalarını koru**  >  **devre dışı bırak** |
|||

Tetikleyici yeni bir dosya bulduğunda, tetikleyici yeni dosyanın tamamlandığını ve kısmen yazılmadığını denetler. Örneğin, tetikleyici dosya sunucusunu denetlerken bir dosya sürmekte olan değişiklikler olabilir. Kısmen yazılmış bir dosyanın döndürülmemek için tetikleyici, son değişiklikleri olan dosyanın zaman damgasını Not etmez, ancak bu dosyayı hemen döndürmez. Tetikleyici dosyayı yalnızca sunucuyu yoklayarak geri döndürür. Bazen bu davranış, tetikleyicinin yoklama aralığı iki katına varan bir gecikmeye neden olabilir.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Yineleme kaydırma ve DRFT tetikleme

Önce bir bağlantı oluşturmanız gereken, SFTP-SSH tetikleyicisi gibi bağlantı tabanlı tetikleyiciler, [yineleme tetikleyicisi](../connectors/connectors-native-recurrence.md)gibi Azure Logic Apps yerel olarak çalışan yerleşik tetikleyicilerden farklıdır. Yinelenen bağlantı tabanlı tetikleyicilerle, yineleme zamanlaması, yürütmeyi denetleyen tek sürücü değildir ve saat dilimi yalnızca ilk başlangıç saatini belirler. Sonraki çalıştırmalar yineleme zamanlaması, son tetikleme yürütmesi *ve* çalışma sürelerinin veya beklenmeyen davranışlara neden olabilecek diğer faktörlere bağlıdır. Örneğin, beklenmedik davranış gün ışığından yararlanma saati (DST) başladığında ve sona erdiğinde belirtilen zamanlamayı sürdürmek için hata içerebilir. DST etkin olduğunda yinelenme saatinin kaydırılamaz olduğundan emin olmak için yinelemeyi el ile ayarlayın. Bu şekilde, iş akışınız beklenen zamanda çalışmaya devam eder. Aksi takdirde, DST başlatıldığında başlangıç saati bir saat ileri ve DST sona erdiğinde bir saat geriye geçer. Daha fazla bilgi için bkz. [bağlantı tabanlı tetikleyiciler Için yinelenme](../connectors/apis-list.md#recurrence-for-connection-based-triggers).

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>PuTTY tabanlı anahtarı OpenSSH 'ye Dönüştür

PuTTY biçimi ve OpenSSH biçimi farklı dosya adı uzantıları kullanır. PuTTY biçimi,. PPK veya PuTTY özel anahtarını, dosya adı uzantısını kullanır. OpenSSH biçimi. pek veya gizlilik Gelişmiş posta, dosya adı uzantısını kullanır. Özel anahtarınız PuTTY biçimindeyse ve OpenSSH biçimini kullanmanız gerekiyorsa, önce aşağıdaki adımları izleyerek anahtarı OpenSSH biçimine dönüştürün:

### <a name="unix-based-os"></a>UNIX tabanlı işletim sistemi

1. Sisteminizde PuTTY araçları yüklü değilse, bunu şimdi yapın, örneğin:

   `sudo apt-get install -y putty`

1. SFTP-SSH Bağlayıcısı ile kullanabileceğiniz bir dosya oluşturan bu komutu çalıştırın:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Örnek:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows İşletim Sistemi

1. Henüz yapmadıysanız, [en son PuTTY Oluşturucu (puttygen.exe) aracını indirin](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)ve ardından aracı başlatın.

1. Bu ekranda **Yükle**' yi seçin.

   !["Yükle" yi seçin](./media/connectors-sftp-ssh/puttygen-load.png)

1. PuTTY biçimindeki özel anahtar dosyanıza gidin ve **Aç**' ı seçin.

1. **Dönüşümler** menüsünde, **OpenSSH anahtarını dışarı aktar**' ı seçin.

   !["OpenSSH anahtarını dışarı aktar" ı seçin](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Özel anahtar dosyasını `.pem` dosya adı uzantısıyla kaydedin.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Bu bölümde, bu bağlayıcının Tetikleyicileri ve eylemlerini kullandığınızda gözden geçirilecek noktalar açıklanmaktadır.

<a name="different-folders-trigger-processing-file-storage"></a>

### <a name="use-different-sftp-folders-for-file-upload-and-processing"></a>Karşıya dosya yükleme ve işleme için farklı SFTP klasörleri kullanma

SFTP sunucunuzda, karşıya yüklenen dosyaları depolamak için ayrı klasörler kullanın ve tetikleyicinin bu dosyaları işlenmek üzere izlemesini sağlar. Aksi takdirde tetikleyici tetiklenmez ve tahmin edilemeyecek şekilde davranır, örneğin, tetikleyicinin işlediği rastgele sayıda dosyayı atlar. Ancak bu gereksinim, dosyaları bu klasörler arasında taşımanın bir yolu olması gerektiği anlamına gelir. 

Bu tetikleyici sorun oluşursa, dosyaları tetikleyicinin izlediği klasörden kaldırın ve karşıya yüklenen dosyaları depolamak için farklı bir klasör kullanın.

<a name="create-file"></a>

### <a name="create-file"></a>Dosya oluştur

SFTP sunucunuzda bir dosya oluşturmak için, SFTP-SSH **dosya oluştur** eylemini kullanabilirsiniz. Bu eylem dosyayı oluşturduğunda, Logic Apps hizmeti Ayrıca, dosyanın meta verilerini almak için SFTP sunucunuzu otomatik olarak çağırır. Ancak, Logic Apps hizmeti meta verileri almak için çağrı yapmadan önce yeni oluşturulan dosyayı taşırsanız, bir `404` hata iletisi alırsınız `'A reference was made to a file or folder which does not exist'` . Dosya oluşturulduktan sonra dosyanın meta verilerini okumayı atlamak için, [ **tüm dosya meta verilerini al** özelliğini **Hayır** olarak eklemek ve ayarlamak](#file-does-not-exist)için adımları izleyin.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>SSH ile SFTP 'ye bağlanma

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Boş Logic Apps için, arama kutusuna `sftp ssh` filtreniz olarak girin. Tetikleyiciler listesinde istediğiniz tetikleyiciyi seçin.

   -veya-

   Mevcut Logic Apps için, eylem eklemek istediğiniz son adım altında **yeni adım**' ı seçin. Arama kutusuna `sftp ssh` filtreniz olarak yazın. Eylemler listesi altında istediğiniz eylemi seçin.

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Bağlantınız için gerekli ayrıntıları sağlayın.

   > [!IMPORTANT]
   >
   > **SSH özel anahtar ÖZELLIĞINDE SSH** özel anahtarınızı girdiğinizde, bu özellik için tüm ve doğru değeri sağladığınızdan emin olmanıza yardımcı olan bu ek adımları izleyin. Geçersiz bir anahtar bağlantının başarısız olmasına neden olur.

   Herhangi bir metin düzenleyicisini kullanabilseniz de, bir örnek olarak Notepad.exe kullanarak anahtarınızı doğru şekilde kopyalamayı ve yapıştırmayı gösteren örnek adımlar aşağıda verilmiştir.

   1. SSH özel anahtar dosyanızı bir metin düzenleyicisinde açın. Bu adımlar örnek olarak not defteri 'Ni kullanır.

   1. Not defteri **düzenleme** menüsünde **Tümünü Seç**' i seçin.

   1. Kopyayı **Düzenle**' yi seçin  >  .

   1. SFTP-SSH tetikleyicisi veya eyleminde, tüm kopyalanmış anahtarı, birden çok satırı destekleyen **SSH özel anahtar** özelliğinde *yapıştırın* . **_Anahtarı el ile girmeyin veya düzenleyemezsiniz_**.

1. Bağlantı ayrıntılarını girmeyi tamamladıktan sonra **Oluştur**' u seçin.

1. Şimdi seçtiğiniz tetikleyici veya eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Öbek boyutunu geçersiz kıl

Öbek kullanan varsayılan Uyarlamalı davranışı geçersiz kılmak için 5 MB ile 50 MB arasında bir sabit öbek boyutu belirtebilirsiniz.

1. Eylemin sağ üst köşesinde üç nokta düğmesini (**...**) ve ardından **Ayarlar**' ı seçin.

   ![SFTP-SSH ayarlarını aç](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. **Içerik aktarımı** altında, **öbek boyutu** özelliğinde, için ' den bir tamsayı değeri girin `5` `50` , örneğin: 

   ![Bunun yerine kullanılacak öbek boyutunu belirtin](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. İşiniz bittiğinde **bitti**' yi seçin.

## <a name="examples"></a>Örnekler

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP-SSH tetikleyicisi: bir dosya eklendiğinde veya değiştirildiğinde

Bu tetikleyici bir SFTP sunucusunda bir dosya eklendiğinde veya değiştirildiğinde bir iş akışı başlatır. Örnek olarak, izleme eylemleri, dosya içeriğinin belirtilen ölçütlere uyup uymadığını denetlemek için bir koşul kullanabilir. İçerik koşulu karşılıyorsa, **Dosya Içeriğini al** SFTP-SSH eylemi içeriği alabilir ve daha sonra başka BIR SFTP-SSH eylemi bu dosyayı SFTP sunucusundaki farklı bir klasöre yerleştirebilir.

**Kurumsal örnek**: Bu tetikleyiciyi, Müşteri emirlerini temsil eden yeni dosyalar IÇIN BIR SFTP klasörünü izlemek üzere kullanabilirsiniz. Daha sonra, **Dosya Içeriğini al** gıbı bır SFTP-SSH eylemi kullanarak, daha fazla işleme için siparişin içeriğini alabilir ve bu siparişi bir Siparişler veritabanında depolayalım.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP-SSH eylemi: yolu kullanarak dosya içeriğini al

Bu eylem, dosya yolunu belirterek SFTP sunucusundaki bir dosyanın içeriğini alır. Örneğin, önceki örnekteki tetikleyiciyi ve dosyanın içeriğinin uyması gereken bir koşulu ekleyebilirsiniz. Koşul doğru ise, içeriği alan eylem çalıştırılabilir.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>Sorunları giderme

Bu bölümde, yaygın hataların veya sorunların olası çözümleri açıklanmaktadır.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504 hatası: "bağlı olan taraf bir süre sonra düzgün bir şekilde yanıt vermediği için bağlantı girişimi başarısız oldu veya bağlı ana bilgisayar yanıt vermediğinden" veya "SFTP sunucusuna yönelik Istek" 00:00:30 "saniyeden uzun sürdü"

Mantıksal uygulamanız SFTP sunucusuyla başarıyla bağlantı kuramazsa bu hata oluşabilir. Bu sorunun farklı nedenleri olabilir, bu nedenle şu sorun giderme seçeneklerini deneyin:

* Bağlantı zaman aşımı 20 saniyedir. SFTP sunucunuzun güvenlik duvarları gibi iyi performansa ve ara cihazlara sahip olup olmadığını kontrol edin. 

* Ayarlanmış bir güvenlik duvarınız varsa, **yönetilen BAĞLAYıCı IP** adreslerini onaylanan listeye eklediğinizden emin olun. Mantıksal uygulamanızın bölgesinin IP adreslerini bulmak için bkz. [sınır ve yapılandırma Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses).

* Bu hata zaman zaman oluşuyorsa, SFTP-SSH eyleminde **yeniden deneme ilkesi** ayarını varsayılan dört yeniden denemeden daha yüksek bir yeniden deneme sayısına değiştirin.

* SFTP sunucusunun her IP adresinden gelen bağlantı sayısına bir sınır alıp yerleştirmediğini denetleyin. Bir sınır varsa, eşzamanlı mantıksal uygulama örneklerinin sayısını sınırlamanız gerekebilir.

* Bağlantı kurma maliyetini azaltmak için, SFTP sunucunuzun SSH yapılandırmasında [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) özelliğini bir saatin etrafında artırın.

* Mantıksal uygulama isteğinin SFTP sunucusuna ulaşıp erişmediğini denetlemek için SFTP sunucusu günlüğünü gözden geçirin. Bağlantı sorunu hakkında daha fazla bilgi edinmek için güvenlik duvarınız ve SFTP sunucunuzda bir ağ izlemesi de çalıştırabilirsiniz.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404 hatası: "varolmayan bir dosya veya klasör için bir başvuru yapıldı"

Bu hata, iş akışınız SFTP sunucunuzda SFTP-SSH **oluşturma dosyası** eylemiyle bir dosya oluşturduğunda ve bu dosyayı hemen Logic Apps hizmeti dosyanın meta verilerini almadan önce taşıdıkça meydana gelebilir. İş akışınız **dosya oluştur** eylemini çalıştırdığında, Logic Apps hizmeti, dosyanın meta verilerini almak için SFTP sunucunuzu otomatik olarak çağırır. Ancak, mantıksal uygulamanız dosyayı taşıdıysanız, hata iletisini almak için Logic Apps hizmeti artık dosyayı bulamıyor olabilir `404` .

Dosyanın taşınmasını önlemek veya bu işlemleri ertelerseniz, aşağıdaki adımları izleyerek Dosya oluşturulduktan sonra dosyanın meta verilerini okumayı atlayabilirsiniz:

1. **Dosya oluştur** eyleminde **yeni parametre Ekle** listesini açın, **tüm dosya meta verilerini al** özelliğini seçin ve değeri **Hayır** olarak ayarlayın.

1. Bu dosya meta verilerine daha sonra ihtiyacınız varsa **dosya meta verilerini al** eylemini kullanabilirsiniz.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bu bağlayıcı hakkında, bağlayıcının Swagger dosyasında açıklanan Tetikleyiciler, Eylemler ve sınırlar gibi daha teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/sftpwithssh/)bakın.

> [!NOTE]
> [Tümleştirme hizmeti ortamındaki (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Logic Apps için, bu bağlayıcının Ise etiketli sürümü, bunun yerine [Ise ileti sınırlarını](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) kullanmasını gerektirir.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
