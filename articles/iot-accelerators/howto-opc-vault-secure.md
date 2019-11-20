---
title: OPC Kasası-Azure ile OPC UA cihazları iletişimini güvenli hale getirme | Microsoft Docs
description: OPC UA uygulamalarını kaydetme ve OPC kasası ile OPC UA cihazlarınız için imzalı uygulama sertifikaları verme.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b5c886625c944e2f5501859e78506ca89ec3d765
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71203683"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>OPC Kasası sertifika yönetimi hizmetini kullanma

Bu makalede, uygulamaların nasıl kaydedileceği ve OPC UA cihazlarınız için imzalı uygulama sertifikalarının nasıl giderileceği açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

### <a name="deploy-the-certificate-management-service"></a>Sertifika Yönetimi hizmetini dağıtma

İlk olarak, hizmeti Azure bulutuna dağıtın. Ayrıntılar için bkz. [OPC Kasası sertifika yönetimi hizmetini dağıtma](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Veren CA sertifikası oluşturma

Henüz yapmadıysanız, veren CA sertifikası oluşturun. Ayrıntılar için bkz. [OPC Kasası Için veren sertifikası oluşturma ve yönetme](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Güvenli OPC UA uygulamaları

### <a name="step-1-register-your-opc-ua-application"></a>1\. Adım: OPC UA Uygulamanızı kaydetme 

> [!IMPORTANT]
> Bir uygulamayı kaydettirmek için yazıcı rolü gereklidir.

1. Sertifika hizmetinizi `https://myResourceGroup-app.azurewebsites.net` ' de açın ve oturum açın.
2. **Yeni kaydet**' e gidin. Bir uygulama kaydı için, bir kullanıcının en azından yazıcı rolü atanmış olması gerekir.
2. Giriş formu OPC UA 'daki adlandırma kurallarına uyar. Örneğin, aşağıdaki ekran görüntüsünde OPC UA .NET Standard yığınında [OPC UA başvuru sunucusu](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) örneği için ayarlar gösterilir:

   ![UA başvuru sunucusu kaydının ekran görüntüsü](media/howto-opc-vault-secure/reference-server-registration.png "UA başvuru sunucusu kaydı")

5. Uygulamayı sertifika hizmeti uygulama veritabanına kaydetmek için **Kaydet** ' i seçin. İş akışı, uygulamaya imzalı bir sertifika istemek için kullanıcıyı doğrudan bir sonraki adıma yönlendirir.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>2\. Adım: uygulamanızı CA imzalı bir uygulama sertifikasıyla güvenli hale getirme

Bir sertifika Imzalama Isteği (CSR) tabanlı imzalı bir sertifika vererek OPC UA uygulamanızın güvenliğini sağlayın. Alternatif olarak, PFX veya ped biçiminde yeni bir özel anahtar içeren yeni bir anahtar çifti de isteyebilirsiniz. Uygulamanız için hangi yöntemin desteklendiği hakkında bilgi için OPC UA cihazınızın belgelerine bakın. Genel olarak CSR yöntemi önerilir, çünkü bir özel anahtarın bir kablo üzerinden aktarılmasını gerektirmez.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Yeni bir anahtar çiftiyle yeni bir sertifika isteyin

1. **Uygulamalar**'a gidin.
3. Listelenen bir uygulama için **yeni istek** ' ı seçin.

   ![Yeni sertifika ıste ekran görüntüsü](media/howto-opc-vault-secure/request-new-certificate.png "Yeni sertifika iste")

3. Özel bir anahtar ve uygulamanız için ortak anahtara sahip yeni bir imzalı sertifika istemek üzere **Yeni anahtar çifti ve sertifika iste** ' yi seçin.

   ![Yeni bir anahtar çifti ve sertifika oluştur ekran görüntüsü](media/howto-opc-vault-secure/generate-new-key-pair.png "Yeni anahtar çifti oluştur")

4. Formu bir konu ve etki alanı adlarıyla girin. Özel anahtar için parola ile ped veya PFX ' i seçin. Sertifika isteği oluşturmak için **Yeni bir anahtar çifti oluştur** ' u seçin.

   ![Sertifika Isteği ayrıntılarını görüntüleme ekran görüntüsü](media/howto-opc-vault-secure/approve-reject.png "Sertifikayı onayla")

5. Onay, onaylayan rolüne sahip bir Kullanıcı ve Azure Key Vault oturum açma izinlerine sahip olmalıdır. Tipik iş akışında, onaylayan ve talep sahibi rollerin farklı kullanıcılara atanması gerekir. Anahtar çiftinin ve imzalama işleminin gerçek oluşturma işlemini başlatmak veya iptal etmek için **Onayla** veya **Reddet** ' i seçin. Yeni anahtar çifti, sertifika isteği tarafından indirilene kadar Azure Key Vault oluşturulur ve güvenli bir şekilde depolanır. Ortak anahtarla elde edilen sertifika, CA tarafından imzalanır. Bu işlemlerin tamamlanması birkaç saniye sürebilir.

   ![Sertifika Isteği ayrıntılarının görüntüleme ekran görüntüsü, en altta onay iletisi](media/howto-opc-vault-secure/view-key-pair.png "Anahtar çiftini görüntüle")

7. Elde edilen özel anahtar (PFX veya ped) ve sertifika (DER), ikili dosya indirme olarak seçilen biçimde buradan indirilebilir. Base64 kodlamalı bir sürüm de kullanılabilir, örneğin, sertifikayı kopyalayıp bir komut satırına veya metin girişine yapıştırabilirsiniz. 
8. Özel anahtar indirilip güvenli şekilde depolandıktan sonra **özel anahtarı sil**' i seçebilirsiniz. Ortak anahtara sahip sertifika ileride kullanılmak üzere kullanılabilir kalır.
9. CA imzalı bir sertifikanın kullanılması nedeniyle, CA sertifikası ve sertifika Iptal listesi (CRL) burada da indirilmelidir.

Artık OPC UA cihazına, yeni anahtar çiftinin nasıl uygulanacağını bağımlıdır. Genellikle CA sertifikası ve CRL bir `trusted` klasöre kopyalanır, ancak uygulama sertifikasının ortak ve özel anahtarları sertifika deposundaki bir `own` klasörüne uygulanır. Bazı cihazlarda sertifika güncelleştirmeleri için sunucu gönderimi zaten desteklenir. OPC UA cihazınızın belgelerine bakın.

#### <a name="request-a-new-certificate-with-a-csr"></a>CSR ile yeni bir sertifika isteme 

1. **Uygulamalar**'a gidin.
3. Listelenen bir uygulama için **yeni istek** ' ı seçin.

   ![Yeni sertifika ıste ekran görüntüsü](media/howto-opc-vault-secure/request-new-certificate.png "Yeni sertifika iste")

3. Uygulamanız için yeni imzalı bir sertifika istemek üzere **Imzalama isteğine sahip yeni sertifika iste** ' yi seçin.

   ![Yeni sertifika oluştur ekran görüntüsü](media/howto-opc-vault-secure/generate-new-certificate.png "Yeni sertifika oluştur")

4. Yerel bir dosya seçerek veya bir Base64 kodlamalı CSR 'yi forma yapıştırarak CSR 'yi karşıya yükleyin. **Yeni sertifika oluştur**' u seçin.

   ![Sertifika Isteği ayrıntılarını görüntüleme ekran görüntüsü](media/howto-opc-vault-secure/approve-reject-csr.png "CSR 'yi Onayla")

5. Onay, onaylayan rolüne sahip bir Kullanıcı ve Azure Key Vault oturum açma izinlerine sahip olmalıdır. **Onayla** veya **Reddet** ' i seçerek gerçek imzalama işlemini başlatın veya iptal edin. Ortak anahtarla elde edilen sertifika, CA tarafından imzalanır. Bu işlemin tamamlanması birkaç saniye sürebilir.

   ![Sertifika Isteği ayrıntılarının görüntüleme ekran görüntüsü, en altta onay iletisi](media/howto-opc-vault-secure/view-cert-csr.png "Sertifikayı görüntüle")

6. Elde edilen sertifika (DER), ikili dosya olarak buradan indirilebilir. Base64 kodlamalı bir sürüm de kullanılabilir, örneğin, sertifikayı kopyalayıp bir komut satırına veya metin girişine yapıştırabilirsiniz. 
10. Sertifika indirilip güvenli bir şekilde depolandıktan sonra **sertifikayı Sil**' i seçebilirsiniz.
11. CA imzalı bir sertifikanın kullanılması nedeniyle, CA sertifikası ve CRL de burada indirilmelidir.

Artık OPC UA cihazına göre yeni sertifikayı nasıl uygulayacaksınız. Genellikle, CA sertifikası ve CRL bir `trusted` klasöre kopyalanır, ancak uygulama sertifikası sertifika deposundaki bir `own` klasörüne uygulanır. Bazı cihazlarda sertifika güncelleştirmeleri için sunucu gönderimi zaten desteklenir. OPC UA cihazınızın belgelerine bakın.

### <a name="step-4-device-secured"></a>4\. Adım: cihaz güvenli

OPC UA cihazı, daha fazla yapılandırma olmadan CA imzalı sertifikalarla güvenliği sağlanmış diğer OPC UA cihazlarıyla iletişim kurmaya hazırdır.

## <a name="next-steps"></a>Sonraki adımlar

OPC UA cihazlarını güvenli hale getirmeye artık öğrendiğinize göre şunları yapabilirsiniz:

> [!div class="nextstepaction"]
> [Güvenli sertifika yönetimi hizmetini çalıştırma](howto-opc-vault-secure-ca.md)