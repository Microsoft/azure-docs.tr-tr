---
title: Azure Data Box/Azure Data Box Heavy cihazlarıyla kendi sertifikalarınızı kullanın
description: Data Box veya Data Box Heavy cihazınızda yerel Web Kullanıcı arabirimine ve blog depolamaya erişmek için kendi sertifikalarınızı kullanma.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: 1836ed57305fd7e168961eb81670b56d4ce296cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545182"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Data Box ve Data Box Heavy cihazlarıyla kendi sertifikalarınızı kullanın

Sipariş işleme sırasında, otomatik olarak imzalanan sertifikalar, bir Data Box veya Data Box Heavy cihazının yerel Web Kullanıcı arabirimine ve BLOB depolamaya erişmek için oluşturulur. Aygıtınızla güvenilir bir kanal üzerinden iletişim kurmayı tercih ediyorsanız, kendi sertifikalarınızı kullanabilirsiniz.

Makale, kendi sertifikalarınızı yüklemeyi ve cihazınızı veri merkezine döndürmeden önce varsayılan sertifikalara döndürmeyi açıklar. Ayrıca, sertifika gereksinimlerinin bir özetini verir.

## <a name="about-certificates"></a>Sertifikalar hakkında

Bir sertifika, bir **ortak anahtar** ve bir **sertifika yetkilisi** gibi güvenilir bir üçüncü taraf tarafından **imzalanmış** (doğrulanmış) bir varlık (örneğin, etki alanı adı) arasında bir bağlantı sağlar.  Bir sertifika, güvenilir ortak şifreleme anahtarları dağıtmanın kolay bir yolunu sağlar. Bu şekilde, sertifikalar iletişimin güvenilir olmasını ve doğru sunucuya şifreli bilgiler gönderdiğinizden emin olmanızı sağlar.

Data Box cihazınız ilk yapılandırıldığında otomatik olarak imzalanan sertifikalar otomatik olarak oluşturulur. İsteğe bağlı olarak, kendi sertifikalarınızı getirebilirsiniz. Kendi sertifikalarınızı getirmeyi planlıyorsanız izlemeniz gereken yönergeler vardır.

Data Box veya Data Box Heavy cihazında, iki uç nokta sertifikası türü kullanılır:

- BLOB depolama sertifikası
- Yerel UI sertifikası

### <a name="certificate-requirements"></a>Sertifika gereksinimleri

Sertifikaların aşağıdaki gereksinimleri karşılaması gerekir:

- Uç nokta sertifikasının, `.pfx` verilebildiğinden bir özel anahtarla biçimde olması gerekir.
- Her uç nokta için tek bir sertifika, birden fazla uç nokta için çok etki alanı sertifikası veya joker karakter uç noktası sertifikası kullanabilirsiniz.
- Bir uç nokta sertifikasının özellikleri, tipik bir SSL sertifikasının özelliklerine benzerdir.
- İstemci makinesinde karşılık gelen bir sertifika, DER biçiminde ( `.cer` filename uzantısı) gereklidir.
- Yerel Kullanıcı arabirimi sertifikasını karşıya yükledikten sonra, tarayıcıyı yeniden başlatmanız ve Önbelleği temizlemeniz gerekir. Tarayıcınızla ilgili özel yönergelere bakın.
- Cihaz adı veya DNS etki alanı adı değişirse sertifikaların değiştirilmesi gerekir.
- Uç nokta sertifikaları oluştururken aşağıdaki tabloyu kullanın:

  |Tür |Konu adı (SN)  |Konu alternatif adı (SAN)  |Konu adı örneği |
  |---------|---------|---------|---------|
  |Yerel Kullanıcı arabirimi| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Blob depolama|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |Çoklu SAN tek sertifika|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

Daha fazla bilgi için bkz. [sertifika gereksinimleri](../../articles/databox-online/azure-stack-edge-gpu-certificate-requirements.md).

## <a name="add-certificates-to-device"></a>Cihaza sertifika ekleme

Yerel Web Kullanıcı arabirimine erişmek ve BLOB depolamaya erişmek için kendi sertifikalarınızı kullanabilirsiniz.

> [!IMPORTANT]
> Cihaz adı veya DNS etki alanı değiştirilirse yeni sertifikaların oluşturulması gerekir. Daha sonra istemci sertifikaları ve cihaz sertifikaları yeni cihaz adı ve DNS etki alanıyla güncelleştirilecektir.

Cihazınıza kendi sertifikanızı eklemek için aşağıdaki adımları izleyin:

1. Sertifikaları **Yönet**' e gidin  >  .

   **Ad** , cihaz adını gösterir. **DNS etki alanı** , DNS sunucusunun etki alanı adını gösterir.

   Ekranın alt kısmında şu anda kullanımda olan sertifikalar gösterilmektedir. Yeni bir cihaz için, sipariş işleme sırasında oluşturulan otomatik olarak imzalanan sertifikaları görürsünüz.

   ![Data Box cihazının sertifikalar sayfası](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. **Adı** (Cihaz adı) veya **DNS etki ALANıNı** (cihazın DNS sunucusunun etki alanı) değiştirmeniz gerekiyorsa, sertifikayı eklemeden önce bunu şimdi yapın. Sonra **Uygula**'yı seçin.

   Cihaz adı veya DNS etki alanı adı değişirse sertifikanın değiştirilmesi gerekir.

   ![Data Box için yeni bir cihaz adı ve DNS etki alanı uygulama](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. Sertifika eklemek için **sertifika ekle ' yi seçin ve** **sertifika ekle bölmesini açın** . Ardından, **BLOB depolama** ya da **Yerel Web Kullanıcı arabirimi** olan **sertifika türünü** seçin.

   ![Data Box cihazının sertifikalar sayfasında sertifikalar ekleme paneli](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. Sertifika dosyasını ( `.pfx` biçiminde) seçin ve sertifika verildiğinde ayarlanan parolayı girin. Sonra **& doğrula**' yı seçin.

   ![Data Box blob uç noktası sertifikası ekleme ayarları](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   Sertifika başarıyla eklendikten sonra, **Sertifikalar** ekranında yeni sertifika için parmak izi gösterilir. Sertifikanın durumu **geçerli**.

   ![Başarıyla eklenen geçerli bir yeni sertifika](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. Sertifika ayrıntılarını görmek için sertifika adını seçin ve tıklatın. Sertifikanın geçerlilik tarihi bir yıl sonra dolacak.

   ![Data Box cihazının sertifika ayrıntılarını görüntüleme](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. Yerel Web Kullanıcı arabirimi için sertifikayı değiştirdiyseniz, tarayıcıyı ve ardından yerel Web Kullanıcı arabirimini yeniden başlatmanız gerekir. SSL önbelleği sorunlarından kaçınmak için bu adım gereklidir.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. Yerel Web Kullanıcı arabirimine erişmek için kullanmakta olduğunuz istemci bilgisayara yeni sertifikayı yükler. Yönergeler için bkz. [sertifikaları Istemciye Içeri aktarma](#import-certificates-to-client).


## <a name="import-certificates-to-client"></a>Sertifikaları istemciye aktar

Data Box cihazınıza bir sertifika ekledikten sonra, sertifikayı cihaza erişmek için kullandığınız istemci bilgisayara aktarmanız gerekir. Sertifikayı yerel makine için güvenilen kök sertifika yetkilisi deposuna aktarırsınız.

Bir Windows istemcisinde bir sertifikayı içeri aktarmak için şu adımları izleyin:

1. Dosya Gezgini 'nde, sertifika dosyasına ( `.cer` biçimiyle) sağ tıklayın ve **sertifikayı yükler**' i seçin. Bu eylem, sertifika alma Sihirbazı 'nı başlatır.

    ![Sertifikayı içeri aktar 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. **Depo konumu** Için **yerel makine**' yi seçin ve ardından **İleri**' yi seçin.

    ![Sertifika Içeri aktarma sihirbazında yerel makine depolama konumu olarak seçme](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. **Tüm sertifikaları aşağıdaki depolama alanına yerleştir**' i seçin, **Güvenilen kök sertifika yetkilisi**' ni seçin ve ardından **İleri**' yi seçin.

   ![Sertifika Içeri aktarma sihirbazında güvenilen kök sertifika yetkilileri deposunu seçin](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. Ayarlarınızı gözden geçirin ve **son**' u seçin. İçeri aktarma işleminin başarılı olduğunu söyleyen bir ileti görüntülenir.

   ![Sertifika ayarlarınızı gözden geçirin ve sertifika alma Sihirbazı 'Nı sona erdirin](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>Varsayılan sertifikalara dön

Cihazınızı Azure veri merkezine döndürmeden önce, sipariş işleme sırasında oluşturulan özgün sertifikalara geri dönmeniz gerekir.

Sipariş işleme sırasında oluşturulan sertifikalara dönmek için aşağıdaki adımları izleyin:

1.   >  **Sertifikaları** Yönet ' e gidin ve **sertifikaları yeniden çevir**' i seçin.

   Sertifikaları geri almak, sipariş işleme sırasında oluşturulan otomatik olarak imzalanan sertifikaları kullanmaya geri döner. Kendi sertifikalarınız cihazdan kaldırılır.

   ![Sertifikaları Data Box bir cihaz için yönetme içindeki sertifika alma seçeneği](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. Sertifika yeniden sürümü başarıyla tamamlandıktan sonra, Kapat ' a gidin **veya yeniden başlatın** ve **Yeniden Başlat**' ı seçin. SSL önbelleği sorunlarından kaçınmak için bu adım gereklidir.

   ![Data Box bir cihazdaki sertifikaları geri döndürdükten sonra yerel Web Kullanıcı arabirimini kapatma veya yeniden başlatma](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   Birkaç dakika bekleyin ve sonra yerel Web Kullanıcı arabiriminde tekrar oturum açın.
