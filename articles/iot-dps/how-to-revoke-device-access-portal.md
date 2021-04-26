---
title: Cihazın Azure IoT Hub cihaz sağlama hizmeti 'nden kaydını kaldır
description: Azure IoT Hub cihaz sağlama hizmeti (DPS) ile sağlamayı engellemek için bir cihazı kaydetme
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c75fcd1fd20e41df5018fcaa07fe83051d7e5f1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97740377"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub cihaz sağlama hizmeti 'nden cihaz kaydetme

Cihaz kimlik bilgilerinin doğru yönetimi, IoT çözümleri gibi yüksek profilli sistemler için önemlidir. Bu tür sistemler için en iyi yöntem, kimlik bilgileri ne zaman, paylaşılan erişim imzaları (SAS) belirtecinin veya X. 509.440 sertifikası güvenliğinin tehlikeye atılıp tehlikede olduğu gibi cihazlara erişimin nasıl iptal edileceği konusunda net bir plana sahip olmanızdır. 

Cihaz sağlama hizmeti 'nde kayıt, bir cihazın [sağlanmasını](about-iot-dps.md#provisioning-process)sağlar. Sağlanan bir cihaz, IoT Hub ile kaydedilmiş ve ilk [cihaz ikizi](~/articles/iot-hub/iot-hub-devguide-device-twins.md) durumunu almasına ve telemetri verilerini raporlamaya başlamaya izin veren bir aygıttır. Bu makalede, sağlama hizmeti örneğinizden bir cihazın daha sonra yeniden sağlanmasını önleyecek şekilde nasıl yapılacağı açıklanır.

> [!NOTE] 
> Erişimini iptal ettiğiniz cihazların yeniden deneme ilkesini unutmayın. Örneğin, sonsuz yeniden deneme ilkesine sahip bir cihaz, sağlama hizmeti ile sürekli olarak kaydolmaya çalışabilir. Bu durum hizmet kaynaklarını kullanır ve muhtemelen performansı etkiler.

## <a name="disallow-devices-by-using-an-individual-enrollment-entry"></a>Bireysel kayıt girişi kullanarak cihazlara izin verme

Bireysel kayıtlar tek bir cihaza uygulanır ve X. 509.952 sertifikalarını, TPM onay anahtarlarını (gerçek veya sanal TPM) veya kanıtlama mekanizması olarak SAS belirteçlerini kullanabilir. Bireysel kaydına sahip bir cihaza izin vermek için kayıt girişini devre dışı bırakabilir veya silebilirsiniz. 

Kayıt girişini devre dışı bırakarak cihaza geçici olarak izin vermek için: 

1. Azure portal oturum açın ve sol menüden **tüm kaynaklar** ' ı seçin.
2. Kaynak listesinde, cihazınızın izin vermemesini istediğiniz sağlama hizmetini seçin.
3. Sağlama hizmetinizde kayıtları **Yönet**' i seçin ve sonra **bireysel** kayıtlar sekmesini seçin.
4. İzin vermek istediğiniz cihaz için kayıt girişini seçin. 

    ![Bireysel kayıt bilgilerinizi seçin](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Kayıt sayfanızda, en alta kaydırın ve **girişi etkinleştir** anahtarı Için **devre dışı bırak** ' ı seçin ve ardından **Kaydet**' i seçin.  

   ![Portalda bireysel kayıt girişini devre dışı bırakma](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Kayıt girdisini silerek cihaza kalıcı olarak izin vermek için:

1. Azure portal oturum açın ve sol menüden **tüm kaynaklar** ' ı seçin.
2. Kaynak listesinde, cihazınızın izin vermemesini istediğiniz sağlama hizmetini seçin.
3. Sağlama hizmetinizde kayıtları **Yönet**' i seçin ve sonra **bireysel** kayıtlar sekmesini seçin.
4. İzin vermek istediğiniz cihaz için kayıt girişinin yanındaki onay kutusunu işaretleyin. 
5. Pencerenin en üstündeki **Sil** ' i seçin ve ardından kaydı kaldırmak istediğinizi onaylamak için **Evet** ' i seçin. 

   ![Portalda bireysel kayıt girişini silme](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Yordamı tamamladıktan sonra, girişin ayrı kayıtlar listesinden kaldırıldığını görmeniz gerekir.  

## <a name="disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Bir kayıt grubu kullanarak X. 509.952 ara veya kök CA sertifikasına izin verme

X. 509.440 sertifikaları tipik olarak bir sertifika güven zincirinde düzenlenir. Bir zincirdeki herhangi bir aşamada bir sertifika tehlikeye atılırsa, güven bozulur. Cihaz sağlama hizmeti 'nin cihazları bu sertifikayı içeren herhangi bir zincirde aşağı akış olarak sağlamasını engellemek için sertifikaya izin verilmeli. X. 509.440 sertifikaları ve sağlama hizmeti ile nasıl kullanıldıkları hakkında daha fazla bilgi edinmek için bkz. [X. 509.440 sertifikaları](./concepts-x509-attestation.md#x509-certificates). 

Kayıt grubu, aynı ara veya kök CA tarafından imzalanan X. 509.440 sertifikalarının ortak kanıtlama mekanizmasını paylaşan cihazlar için bir giriştir. Kayıt grubu girişi, ara veya kök CA ile ilişkili X. 509.440 sertifikasıyla yapılandırılır. Giriş Ayrıca, sertifika zincirinde bu sertifikaya sahip cihazlar tarafından paylaşılan ikizi State ve IoT Hub bağlantısı gibi herhangi bir yapılandırma değeri ile yapılandırılır. Sertifikaya izin vermemek için kayıt grubunu devre dışı bırakabilir ya da silebilirsiniz.

Kayıt grubunu devre dışı bırakarak sertifikaya geçici olarak izin vermek için: 

1. Azure portal oturum açın ve sol menüden **tüm kaynaklar** ' ı seçin.
2. Kaynak listesinde, imzalama sertifikasının iznini kaldırmak istediğiniz sağlama hizmetini seçin.
3. Sağlama hizmetinizde kayıtları **Yönet**' i seçin ve ardından **kayıt grupları** sekmesini seçin.
4. İzin vermek istediğiniz sertifikayı kullanarak kayıt grubunu seçin.
5. **Girişi etkinleştir** anahtarından **devre dışı bırak** ' ı seçin ve ardından **Kaydet**' i seçin.  

   ![Portalda kayıt grubu girişini devre dışı bırakma](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Kayıt grubunu silerek sertifikaya kalıcı olarak izin vermek için:

1. Azure portal oturum açın ve sol menüden **tüm kaynaklar** ' ı seçin.
2. Kaynak listesinde, cihazınızın izin vermemesini istediğiniz sağlama hizmetini seçin.
3. Sağlama hizmetinizde kayıtları **Yönet**' i seçin ve ardından **kayıt grupları** sekmesini seçin.
4. İzin vermek istediğiniz sertifika için kayıt grubunun yanındaki onay kutusunu işaretleyin. 
5. Pencerenin üstündeki **Sil** ' i seçin ve ardından kayıt grubunu kaldırmak istediğinizi onaylamak için **Evet** ' i seçin. 

   ![Portalda kayıt grubu girişini silme](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Yordamı tamamladıktan sonra, girişin kayıt grupları listesinden kaldırıldığını görmeniz gerekir.  

> [!NOTE]
> Bir sertifika için bir kayıt grubunu silerseniz, sertifika zincirinde sertifikaya sahip olan cihazlar, kök sertifikaya yönelik etkinleştirilmiş bir kayıt grubu veya sertifika zincirinde daha yüksek olan başka bir ara sertifika varsa yine de kayıt yapabiliyor olabilir.

## <a name="disallow-specific-devices-in-an-enrollment-group"></a>Kayıt grubundaki belirli cihazlara izin verme

X. 509.440 kanıtlama mekanizmasını uygulayan cihazlar, kimlik doğrulaması için cihazın sertifika zincirini ve özel anahtarını kullanır. Cihaz sağlama hizmeti ile bir cihaz bağlanıp kimlik doğrulaması yapıldığında, hizmet önce cihazın kimlik bilgileriyle eşleşen tek bir kayıt arar. Hizmet daha sonra, cihazın sağlanıp sağlanamayacağını öğrenmek için kayıt gruplarını arar. Hizmet, cihaz için devre dışı bir bireysel kayıt bulursa cihazın bağlanmasını engeller. Cihazın sertifika zincirindeki bir ara veya kök CA için etkinleştirilmiş bir kayıt grubu mevcut olsa bile hizmet bağlantıyı engeller. 

Kayıt grubundaki tek bir cihaza izin vermemek için şu adımları izleyin:

1. Azure portal oturum açın ve sol menüden **tüm kaynaklar** ' ı seçin.
2. Kaynak listesinden, izin vermek istediğiniz cihaz için kayıt grubunu içeren sağlama hizmeti ' ni seçin.
3. Sağlama hizmetinizde kayıtları **Yönet**' i seçin ve sonra **bireysel** kayıtlar sekmesini seçin.
4. Üst kısımdaki **tek kayıt Ekle** düğmesini seçin. 
5. **Kayıt Ekle** sayfasında, cihaz Için kanıtlama **mekanizması** olarak **X. 509.440** ' yi seçin.

    Cihaz sertifikasını karşıya yükleyin ve izin verilmeyen cihazın cihaz KIMLIĞINI girin. Sertifika için, cihazda yüklü imzalı son varlık sertifikasını kullanın. Cihaz, kimlik doğrulaması için imzalı son varlık sertifikasını kullanır.

    ![İzin verilmeyen cihaz için cihaz özelliklerini ayarla](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. **Kayıt Ekle** sayfasının en altına gidin ve **girişi etkinleştir** anahtarından **devre dışı bırak** ' ı seçin ve ardından **Kaydet**' i seçin. 

    [![Portalda grup kaydından cihazı devre dışı bırakmak için devre dışı bireysel kayıt girişini kullanın](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Kaydınıza başarıyla bağlandığınızda, **bireysel** kayıtlar sekmesinde devre dışı bırakılmış cihaz kaydını görmeniz gerekir. 

## <a name="next-steps"></a>Sonraki adımlar

Kayıt kaldırma işlemi, daha büyük sağlama işleminin de bir parçasıdır. Bir cihazın sağlamasını kaldırma işlemi, sağlama hizmetinden ve IoT Hub 'ından kaydını kaldırmak içerir. Tam işlem hakkında bilgi edinmek için bkz. [daha önce otomatik olarak sağlanan cihazların sağlamasını kaldırma](how-to-unprovision-devices.md)
