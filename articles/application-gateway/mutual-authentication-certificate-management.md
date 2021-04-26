---
title: İstemci kimlik doğrulaması için güvenilen istemci CA sertifika zincirini dışarı aktarma
titleSuffix: Azure Application Gateway
description: Azure Application Gateway istemci kimlik doğrulaması için güvenilir bir istemci CA sertifika zincirini dışarı aktarmayı öğrenin
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 2329dc7426b223ef2c81dd0e2e607bccf73192e6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231633"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>İstemci kimlik doğrulamasıyla kullanılacak güvenilir bir istemci CA sertifika zincirini dışarı aktarma
İstemci veya istemci kimlik doğrulaması ile karşılıklı kimlik doğrulaması yapılandırmak için Application Gateway, bir güvenilen istemci CA Sertifika zincirinin ağ geçidine yüklenmesini gerektirir. Birden çok sertifika zincirinizi varsa, zincirlerini ayrı olarak oluşturmanız ve Application Gateway farklı dosyalar olarak yüklemeniz gerekir. Bu makalede, ağ geçidinizdeki istemci kimlik doğrulama yapılandırmanızda kullanabileceğiniz bir güvenilen istemci CA sertifika zincirini nasıl dışarı aktaracağınızı öğreneceksiniz.  

## <a name="prerequisites"></a>Önkoşullar

Güvenilen istemci CA sertifika zincirini oluşturmak için var olan bir istemci sertifikası gereklidir. 

## <a name="export-trusted-client-ca-certificate"></a>Güvenilen istemci CA sertifikasını dışarı aktar

Application Gateway üzerinde istemci kimlik doğrulamasına izin vermek için güvenilen istemci CA sertifikası gereklidir. Bu örnekte, istemci sertifikası için bir TLS/SSL sertifikası kullanacağız, ortak anahtarını dışa aktarabilir ve ardından Güvenilen istemci CA sertifikalarını almak için ortak anahtardan CA sertifikalarını dışarı aktaracağız. Daha sonra tüm istemci CA sertifikalarını bir güvenilen istemci CA sertifika zincirinde birleştirme yapacağız. 

Aşağıdaki adımlar, sertifikanız için. ped veya. cer dosyasını dışarı aktarmaya yardımcı olur:

### <a name="export-public-certificate"></a>Ortak sertifikayı dışarı aktar 

1. Sertifikadan bir .cer dosyası almak için **Kullanıcı sertifikalarını yönet** menüsünü açın. Sertifikayı, genellikle ' sertifikalar-geçerli kullanıcı \ kişisel \ Sertifikalar ' içinde bulun ve sağ tıklayın. **Tüm Görevler**’e tıklayın ve ardından **Dışarı Aktar**’a tıklayın. **Sertifika Dışarı Aktarma Sihirbazı** açılır. Geçerli Kullanıcı \ kişisel \ sertifikalar altındaki sertifikayı bulamazsanız, yanlışlıkla "Sertifikalar-Geçerli Kullanıcı" yerine "Sertifikalar-Yerel bilgisayar" seçeneğini açmış olabilirsiniz. PowerShell kullanarak geçerli kullanıcı kapsamında sertifika yöneticisi 'ni açmak istiyorsanız konsol penceresine *certmgr* yazın.

    > [!div class="mx-imgBorder"]
    > ![Ekran görüntüsü, seçili sertifikaların bulunduğu sertifika yöneticisini ve tüm görevleri içeren bir bağlamsal menüyü gösterir ve ardından seçili olarak dışa aktarın.](./media/certificates-for-backend-authentication/export.png)

2. Sihirbazda, **İleri**' ye tıklayın.
    > [!div class="mx-imgBorder"]
    > ![Sertifikayı dışarı aktarma](./media/certificates-for-backend-authentication/exportwizard.png)

3. **Hayır, özel anahtarı dışarı aktarma**’yı seçin ve **İleri**’ye tıklayın.
    > [!div class="mx-imgBorder"]
    > ![Özel anahtarı dışarı aktarma](./media/certificates-for-backend-authentication/notprivatekey.png)

4. **Dışarı Aktarma Dosyası Biçimi** sayfasında **Base-64 ile kodlanmış X.509 (.CER)** seçeneğini belirleyin ve **İleri**’ye tıklayın.
    > [!div class="mx-imgBorder"]
    > ![Base-64 kodlu](./media/certificates-for-backend-authentication/base64.png)

5. **Dışarı aktarılacak dosya** için, sertifikayı dışarı aktarmak istediğiniz konuma **gidin** . **Dosya adı** alanına, sertifika dosyası için bir ad girin. Ardından **İleri**' ye tıklayın.

    > [!div class="mx-imgBorder"]
   > ![Ekran görüntüsü, dışarı aktarılacak bir dosyayı belirttiğiniz sertifika dışarı aktarma Sihirbazı ' nı gösterir.](./media/certificates-for-backend-authentication/browse.png)

6. Sertifikayı dışarı aktarmak için **Son**'a tıklayın.

    > [!div class="mx-imgBorder"]
    > ![Dosya dışarı aktarmayı tamamladıktan sonra, ekran görüntüsü sertifika dışarı aktarma Sihirbazı ' nı gösterir.](./media/certificates-for-backend-authentication/finish.png)

7. Sertifikanız başarıyla verildi.

    > [!div class="mx-imgBorder"]
    > ![Ekran görüntüsü, bir başarı iletisi ile sertifika dışarı aktarma Sihirbazı 'nı gösterir.](./media/certificates-for-backend-authentication/success.png)

   Verdiğiniz sertifika şuna benzer:

    > [!div class="mx-imgBorder"]
    > ![Ekran görüntüsünde bir sertifika simgesi gösterilir.](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>CA sertifikalarını ortak sertifikadan dışarı aktar

Genel sertifikanızı dışarı aktardığınıza göre artık CA sertifikalarını ortak sertifikaınızdan dışarı aktarmış olursunuz. Yalnızca bir kök CA 'nız varsa yalnızca bu sertifikayı dışarı aktarmanız gerekir. Ancak, 1 + ara CAs varsa, bunların her birini de dışarı aktarmanız gerekir. 

1. Ortak anahtar verildikten sonra dosyayı açın.

    > [!div class="mx-imgBorder"]
    > ![Yetkilendirme Sertifikası aç](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![Sertifika hakkında](./media/mutual-authentication-certificate-management/general.png)

1. Sertifika yetkilisini görüntülemek için sertifika yolu sekmesini seçin.

    > [!div class="mx-imgBorder"]
    > ![sertifika ayrıntıları](./media/mutual-authentication-certificate-management/cert-details.png) 

1. Kök sertifikayı seçin ve **sertifikayı görüntüle**' ye tıklayın.

    > [!div class="mx-imgBorder"]
    > ![Sertifika yolu](./media/mutual-authentication-certificate-management/root-cert.png) 

   Kök sertifika ayrıntılarını görmeniz gerekir.

    > [!div class="mx-imgBorder"]
    > ![Sertifika bilgileri](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. **Ayrıntılar** sekmesini seçin ve **Dosyaya Kopyala...** seçeneğine tıklayın.

    > [!div class="mx-imgBorder"]
    > ![kök sertifikayı Kopyala](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. Bu noktada, kök CA sertifikasının ayrıntılarını genel sertifikadan ayıkladık. **Sertifika dışarı aktarma Sihirbazı**'nı görürsünüz. Sertifika Dışarı Aktarma Sihirbazı 'nı gerçekleştirmek için önceki bölümde ([ortak sertifikayı dışarı aktarma](./mutual-authentication-certificate-management.md#export-public-certificate)) 2-7 adımlarını izleyin. 

1. Şimdi bu geçerli bölümden 2-6 adımları yineleyin ([genel SERTIFIKADAKI CA sertifikalarını dışarı aktarın](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate)) ve tüm ara CA sertifikalarını, Base-64 kodlu X. 509.440 (. CER) biçiminde.

    > [!div class="mx-imgBorder"]
    > ![ara sertifika](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    Örneğin, *MSIT CAZ2* ara CA 'daki bu bölümden 2-6 arası adımları tekrarlayarak kendi sertifikası olarak ayıklayın. 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>Tüm CA sertifikalarınızı tek bir dosyada birleştirme

1. Daha önce ayıkladığınız tüm CA sertifikalarıyla aşağıdaki komutu çalıştırın. 

    Windows:
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    Linux:
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    Elde edilen birleştirilmiş sertifikanız aşağıdakine benzer şekilde görünmelidir:
    
    > [!div class="mx-imgBorder"]
    > ![Birleşik sertifika](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık güvenilen istemci CA sertifika zincirine sahipsiniz. Bunu, ağ geçidiniz ile karşılıklı kimlik doğrulamasına izin vermek için Application Gateway istemci kimlik doğrulama yapılandırmanıza ekleyebilirsiniz. Bkz. [Application Gateway kullanarak karşılıklı kimlik doğrulaması yapılandırma](./mutual-authentication-portal.md) veya [PowerShell ile Application Gateway kullanarak karşılıklı kimlik doğrulaması yapılandırma](./mutual-authentication-powershell.md).

