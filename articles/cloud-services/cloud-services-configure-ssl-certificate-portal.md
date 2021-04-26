---
title: Bir bulut hizmeti için TLS Yapılandırma | Microsoft Docs
description: Bir Web rolü için HTTPS uç noktası belirtme ve uygulamanızın güvenliğini sağlamak için TLS/SSL sertifikası yükleme hakkında bilgi edinin. Bu örnekler Azure portal kullanır.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 33aa088efd7768153d4a17472d82e0826f4ffa6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742650"
---
# <a name="configuring-tls-for-an-application-in-azure"></a>Azure 'da bir uygulama için TLS Yapılandırma

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Daha önce Güvenli Yuva Katmanı (SSL) şifrelemesi olarak bilinen Aktarım Katmanı Güvenliği (TLS), internet üzerinden gönderilen verilerin güvenliğini sağlamak için en yaygın kullanılan yöntemdir. Bu ortak görevde bir Web rolü için HTTPS uç noktasının nasıl kullanılacağı ve uygulamanızın güvenliğini sağlamak için bir TLS/SSL sertifikasının nasıl yükleneceği açıklanır.

> [!NOTE]
> Bu görevdeki yordamlar Azure Cloud Services için geçerlidir; Uygulama Hizmetleri için [bkz..](../app-service/configure-ssl-bindings.md)
>

Bu görev bir üretim dağıtımı kullanır. Hazırlama dağıtımı kullanmayla ilgili bilgiler bu konunun sonunda verilmiştir.

Henüz bir bulut hizmeti oluşturmadıysanız [bunu](cloud-services-how-to-create-deploy-portal.md) okuyun.

## <a name="step-1-get-a-tlsssl-certificate"></a>1. Adım: bir TLS/SSL sertifikası edinme
Bir uygulama için TLS yapılandırmak üzere, bu amaçla sertifika veren güvenilir bir üçüncü taraf olan bir sertifika yetkilisi (CA) tarafından imzalanmış bir TLS/SSL sertifikası almanız gerekir. Henüz bir tane yoksa, TLS/SSL sertifikaları satan bir şirketten bir tane edinmeniz gerekir.

Sertifika, Azure 'daki TLS/SSL sertifikaları için aşağıdaki gereksinimleri karşılamalıdır:

* Sertifika, ortak anahtar içermelidir.
* Sertifika, anahtar değişimi için, kişisel bilgi değişimi (. pfx) dosyasına verilebilir şekilde oluşturulmalıdır.
* Sertifikanın konu adı, bulut hizmetine erişmek için kullanılan etki alanı ile aynı olmalıdır. Cloudapp.net etki alanı için bir sertifika yetkilisinden (CA) bir TLS/SSL sertifikası edinemezsiniz. Hizmetinize erişirken kullanmak için özel bir etki alanı adı almanız gerekir. CA 'dan bir sertifika istediğinizde, sertifikanın konu adı, uygulamanıza erişmek için kullanılan özel etki alanı adıyla aynı olmalıdır. Örneğin, özel etki alanı adınız **contoso.com** ise, "*_. contoso.com_* veya **www \. contoso.com** için CA 'nızdan bir sertifika istemeniz gerekir.
* Sertifika en az 2048 bit şifreleme kullanmalıdır.

Test amaçları için otomatik olarak imzalanan bir sertifika [oluşturup](cloud-services-certs-create.md) kullanabilirsiniz. Otomatik olarak imzalanan bir sertifika, CA aracılığıyla kimlik doğrulaması içermez ve cloudapp.net etki alanını Web sitesi URL 'SI olarak kullanabilir. Örneğin, aşağıdaki görev, sertifikada kullanılan ortak adın (CN) **sslexample.cloudapp.net** olduğu kendinden imzalı bir sertifika kullanır.

Daha sonra, hizmet tanımınızda sertifika ile ilgili bilgileri ve hizmet yapılandırma dosyalarını dahil etmeniz gerekir.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>2. Adım: hizmet tanımını ve yapılandırma dosyalarını değiştirme
Uygulamanızın sertifikayı kullanacak şekilde yapılandırılması ve bir HTTPS uç noktasının eklenmesi gerekir. Sonuç olarak, hizmet tanımı ve hizmet yapılandırma dosyalarının güncellenmesi gerekir.

1. Geliştirme ortamınızda, hizmet tanım dosyası (CSDEF) ' nı açın, **WebRole** bölümü Içinde bir **Sertifikalar** bölümü ekleyin ve sertifika (ve ara sertifikalar) hakkında aşağıdaki bilgileri ekleyin:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   **Sertifikalar** bölümü, sertifikamızın adını, konumunu ve bulunduğu deponun adını tanımlar.

   İzinler ( `permissionLevel` öznitelik) aşağıdaki değerlerden birine ayarlanabilir:

   | İzin değeri | Description |
   | --- | --- |
   | Limitedoretavated |**(Varsayılan)** Tüm rol işlemlerinde özel anahtara erişim sağlayabilirsiniz. |
   | kaynağa |Yalnızca yükseltilmiş süreçler özel anahtara erişebilir. |

2. Hizmet tanımı dosyanızda, HTTPS 'yi etkinleştirmek için **uç noktalar** bölümüne bir **ınputendpoint** öğesi ekleyin:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. Hizmet tanımı dosyanızda, **siteler** bölümünün Içine bir **bağlama** öğesi ekleyin. Bu öğe, uç noktasını sitenize eşlemek için bir HTTPS bağlaması ekler:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Hizmet tanımı dosyasında gerekli tüm değişiklikler tamamlandı; Ancak, yine de sertifika bilgilerini hizmet yapılandırma dosyasına eklemeniz gerekir.
4. Hizmet yapılandırma dosyanızda (CSCFG), ServiceConfiguration. Cloud. CSCFG ' de, sertifikanıza sahip bir **sertifika** değeri ekleyin. Aşağıdaki kod örneği, parmak izi değeri dışında **Sertifikalar** bölümünün ayrıntılarını sağlar.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(Bu örnek, parmak izi algoritması için **SHA1** kullanır. Sertifikanızın parmak izi algoritmanız için uygun değeri belirtin.)

Artık hizmet tanımı ve hizmet yapılandırma dosyaları güncelleştirildiğinden, dağıtımınızı Azure 'a yüklemek için paketleyin. **Cspack** kullanıyorsanız, az önce eklediğiniz sertifika bilgilerinin üzerine yazılacak şekilde **/Generateconfigurationfile** bayrağını kullanmayın.

## <a name="step-3-upload-a-certificate"></a>3. Adım: bir sertifikayı karşıya yükleme
Azure portal Bağlan ve...

1. Portalın **tüm kaynaklar** bölümünde bulut hizmetinizi seçin.

    ![Bulut hizmetinizi yayımlayın](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. **Sertifikalar**’a tıklayın.

    ![Sertifikalar simgesine tıklayın](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Sertifikalar alanının en üstünde **karşıya yükle** ' ye tıklayın.

    ![Karşıya Yükle menü öğesine tıklayın](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. **Dosyayı**, **parolayı** girin ve ardından veri girişi alanının altındaki **karşıya yükle** ' ye tıklayın.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>4. Adım: rol örneğine HTTPS kullanarak bağlanma
Artık dağıtımınız Azure 'da çalışır durumda olduğundan, HTTPS kullanarak buna bağlanabilirsiniz.

1. Web tarayıcısını açmak için **site URL 'sine** tıklayın.

   ![Site URL 'sine tıklayın](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Web tarayıcınızda, bağlantıyı **http** yerine **https** kullanacak şekilde değiştirin ve ardından sayfayı ziyaret edin.

   > [!NOTE]
   > Kendinden imzalı bir sertifika kullanıyorsanız, otomatik olarak imzalanan sertifikayla ilişkili bir HTTPS uç noktasına gözattığınızda tarayıcıda bir sertifika hatası görebilirsiniz. Güvenilen bir sertifika yetkilisi tarafından imzalanan bir sertifika kullanmak bu sorunu ortadan kaldırır; Bu sırada, hatayı yoksayabilirsiniz. (Diğer bir seçenek de kullanıcının güvenilen sertifika yetkilisi sertifika deposuna otomatik olarak imzalanan sertifikayı eklemektir.)
   >
   >

   ![Site önizlemesi](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Bir üretim dağıtımı yerine hazırlama dağıtımı için TLS kullanmak istiyorsanız, ilk olarak hazırlama dağıtımı için kullanılan URL 'YI belirlemeniz gerekir. Bulut hizmetiniz dağıtıldıktan sonra, hazırlama ortamının URL 'SI **DAĞıTıM kimliği** GUID 'si tarafından bu biçimde belirlenir: `https://deployment-id.cloudapp.net/`  
   >
   > GUID tabanlı URL 'ye (örneğin, **328187776e774ceda8fc57609d404462.cloudapp.net**) eşit ortak ada (CN) sahip bir sertifika oluşturun. Sertifikayı hazırlanan bulut hizmetinize eklemek için portalını kullanın. Daha sonra, CSDEF ve CSCFG dosyalarınıza sertifika bilgilerini ekleyin, uygulamanızı yeniden paketleyin ve hazırlanan dağıtımınızı yeni paketi kullanacak şekilde güncelleştirin.
   >

## <a name="next-steps"></a>Sonraki adımlar
* [Bulut hizmetinizin genel yapılandırması](cloud-services-how-to-configure-portal.md).
* [Bulut hizmetini dağıtmayı](cloud-services-how-to-create-deploy-portal.md)öğrenin.
* Özel bir [etki alanı adı](cloud-services-custom-domain-name-portal.md)yapılandırın.
* [Bulut hizmetinizi yönetin](cloud-services-how-to-manage-portal.md).



