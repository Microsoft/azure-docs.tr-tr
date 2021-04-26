---
title: Portalı kullanarak uçtan uca TLS şifrelemesini yapılandırma
titleSuffix: Azure Application Gateway
description: Uçtan uca TLS şifrelemesi ile uygulama ağ geçidi oluşturmak için Azure portal nasıl kullanacağınızı öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6e2d1828acefacb03cc2f42193b8cd8897578b6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397510"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Portalla Application Gateway'i kullanarak uçtan uca TLS'yi yapılandırma

Bu makalede, Azure Application Gateway v1 SKU 'SU aracılığıyla daha önce Güvenli Yuva Katmanı (SSL) şifreleme olarak bilinen uçtan uca Aktarım Katmanı Güvenliği (TLS) şifrelemesini yapılandırmak için Azure portal nasıl kullanılacağı açıklanır.

> [!NOTE]
> Application Gateway v2 SKU 'SU, uçtan uca yapılandırmayı etkinleştirmek için güvenilen kök sertifikaları gerektirir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

Bir Application Gateway ile uçtan uca TLS yapılandırmak için ağ geçidi için bir sertifika gerekir. Ayrıca, arka uç sunucuları için sertifikalar gerekir. Ağ Geçidi sertifikası, TLS protokol belirtimi ile uyumlu bir simetrik anahtar türetmek için kullanılır. Daha sonra simetrik anahtar, ağ geçidine gönderilen trafiği şifrelemek ve şifresini çözmek için kullanılır. 

Uçtan uca TLS şifrelemesi için, uygulama ağ geçidinde doğru arka uç sunucularına izin verilmelidir. Bu erişime izin vermek için, arka uç sunucularının, kimlik doğrulama sertifikaları (v1) veya güvenilir kök sertifikalar (v2) olarak da bilinen genel sertifikasını uygulama ağ geçidine yükleyin. Sertifikayı eklemek, Application Gateway 'in yalnızca bilinen arka uç örnekleriyle iletişim kuracağını sağlar. Bu yapılandırma uçtan uca iletişimin güvenliğini sağlar.

Daha fazla bilgi edinmek için bkz. [Application Gateway Ile TLS sonlandırmasına ve uçtan uca TLS 'ye genel bakış](./ssl-overview.md).

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Uçtan uca TLS ile yeni bir uygulama ağ geçidi oluşturma

Uçtan uca TLS şifrelemesi ile yeni bir uygulama ağ geçidi oluşturmak için, yeni bir uygulama ağ geçidi oluştururken öncelikle TLS sonlandırmasını etkinleştirmeniz gerekir. Bu eylem, istemci ve uygulama ağ geçidi arasındaki iletişim için TLS şifrelemesini mümkün bir şekilde sunar. Daha sonra, HTTP ayarlarındaki arka uç sunucularının sertifikalarını güvenli alıcılara yerleştirmeniz gerekir. Bu yapılandırma, uygulama ağ geçidi ve arka uç sunucuları arasındaki iletişim için TLS şifrelemesini mümkün bir şekilde sunar. Bu, uçtan uca TLS şifrelemesini gerçekleştirir.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Yeni bir uygulama ağ geçidi oluştururken TLS sonlandırmasını etkinleştir

Daha fazla bilgi edinmek için bkz. [Yeni bir uygulama ağ geçidi oluştururken TLS sonlandırmayı etkinleştirme](./create-ssl-portal.md).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Arka uç sunucularının kimlik doğrulama/kök sertifikalarını ekleyin

1. **Tüm kaynaklar**' ı ve ardından **myappgateway**' i seçin.

2. Sol taraftaki menüden **http ayarları** ' nı seçin. Azure, uygulama ağ geçidini oluştururken **Appgatewaybackendhttpsettings** varsayılan HTTP ayarını otomatik olarak oluşturdu. 

3. **Appgatewaybackendhttpsettings** öğesini seçin.

4. **Protokol** altında **https**' yi seçin. **Arka uç kimlik doğrulama sertifikaları veya güvenilen kök sertifikaların** bölmesi görüntülenir.

5. **Yeni oluştur**’u seçin.

6. **Ad** alanına uygun bir ad girin.

7. **Cer sertifikasını karşıya yükle** kutusunda sertifika dosyasını seçin.

   Standart ve WAF (v1) uygulama ağ geçitleri için arka uç sunucu sertifikanızın ortak anahtarını. cer biçiminde yüklemeniz gerekir.

   ![Sertifika ekleme](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 ve WAF_v2 uygulama ağ geçitleri için, arka uç sunucu sertifikasının kök sertifikasını. cer biçiminde yüklemeniz gerekir. Arka uç sertifikası iyi bilinen bir sertifika yetkilisi (CA) tarafından verildiyse, **Iyi BILINEN CA sertifikasını kullan** onay kutusunu seçebilir ve ardından bir sertifikayı karşıya yüklemeniz gerekmez.

   ![Güvenilen kök sertifika ekle](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Kök sertifika](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. **Kaydet**’i seçin.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Mevcut uygulama ağ geçidi için uçtan uca TLS 'yi etkinleştirme

Mevcut bir uygulama ağ geçidini uçtan uca TLS şifrelemesiyle yapılandırmak için, önce dinleyicide TLS sonlandırmasını etkinleştirmeniz gerekir. Bu eylem, istemci ve uygulama ağ geçidi arasındaki iletişim için TLS şifrelemesini sunar. Daha sonra, bu sertifikaları, Güvenli Alıcılar listesindeki HTTP ayarlarındaki arka uç sunucuları için koyun. Bu yapılandırma, uygulama ağ geçidi ve arka uç sunucuları arasındaki iletişim için TLS şifrelemesini mümkün bir şekilde sunar. Bu, uçtan uca TLS şifrelemesini gerçekleştirir.

TLS sonlandırmasını etkinleştirmek için HTTPS protokolü ve bir sertifika ile bir dinleyici kullanmanız gerekir. Bu koşulları karşılayan mevcut bir dinleyiciyi kullanabilir ya da yeni bir dinleyici oluşturabilirsiniz. Önceki seçeneği belirlerseniz, aşağıdaki "var olan bir uygulama ağ geçidinde TLS sonlandırmasını etkinleştir" bölümünü yoksayabilirsiniz ve doğrudan "arka uç sunucuları için kimlik doğrulaması/güvenilen kök sertifikaları Ekle" bölümüne geçebilirsiniz.

İkinci seçeneği belirlerseniz, adımları aşağıdaki yordamda uygulayın.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Mevcut bir uygulama ağ geçidinde TLS sonlandırmasını etkinleştirme

1. **Tüm kaynaklar**' ı ve ardından **myappgateway**' i seçin.

2. Sol taraftaki menüden **dinleyicileri** seçin.

3. Gereksinimlerinize bağlı olarak **temel** veya **çok** siteli dinleyici ' i seçin.

4. **Protokol** altında **https**' yi seçin. Bir **sertifika** bölmesi görüntülenir.

5. İstemci ve uygulama ağ geçidi arasında TLS sonlandırma için kullanmayı düşündüğünüz PFX sertifikasını karşıya yükleyin.

   > [!NOTE]
   > Sınama amacıyla, kendinden imzalı bir sertifika kullanabilirsiniz. Ancak, bu, üretim iş yükleri için önerilmez çünkü yönetilmesi zordur ve tamamen güvende değildir. Daha fazla bilgi için bkz. [otomatik olarak imzalanan sertifika oluşturma](./create-ssl-portal.md#create-a-self-signed-certificate).

6. Gereksinimlerinize bağlı olarak, **dinleyici** için gereken diğer ayarları ekleyin.

7. Kaydetmek için **Tamam**’ı seçin.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Arka uç sunucularının kimlik doğrulaması/güvenilen kök sertifikalarını ekleyin

1. **Tüm kaynaklar**' ı ve ardından **myappgateway**' i seçin.

2. Sol taraftaki menüden **http ayarları** ' nı seçin. Sertifikaları, Güvenli Alıcılar listesinde var olan bir arka uç HTTP ayarına yerleştirebilir veya yeni bir HTTP ayarı oluşturabilirsiniz. (Bir sonraki adımda, **Appgatewaybackendhttpsettings** varsayılan http ayarının sertifikası Güvenli Alıcılar listesine eklenir.)

3. **Appgatewaybackendhttpsettings** öğesini seçin.

4. **Protokol** altında **https**' yi seçin. **Arka uç kimlik doğrulama sertifikaları veya güvenilen kök sertifikaların** bölmesi görüntülenir. 

5. **Yeni oluştur**’u seçin.

6. **Ad** alanına uygun bir ad girin.

7. **Cer sertifikasını karşıya yükle** kutusunda sertifika dosyasını seçin.

   Standart ve WAF (v1) uygulama ağ geçitleri için arka uç sunucu sertifikanızın ortak anahtarını. cer biçiminde yüklemeniz gerekir.

   ![Sertifika ekleme](./media/end-to-end-ssl-portal/addcert.png)

   Standard_v2 ve WAF_v2 uygulama ağ geçitleri için, arka uç sunucu sertifikasının kök sertifikasını. cer biçiminde yüklemeniz gerekir. Arka uç sertifikası iyi bilinen bir CA tarafından verildiyse, **Iyi BILINEN CA sertifikasını kullan** onay kutusunu seçebilirsiniz ve ardından bir sertifikayı karşıya yüklemeniz gerekmez.

   ![Güvenilen kök sertifika ekle](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure CLI kullanarak bir uygulama ağ geçidi ile web trafiğini yönetme](./tutorial-manage-web-traffic-cli.md)