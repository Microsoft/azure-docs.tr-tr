---
title: Azure Stack Edge Mini R cihazını dağıtmaya Azure portal, Datacenter ortamını hazırlama öğreticisi | Microsoft Docs
description: Azure Stack Edge Mini R cihazının dağıtılmasıyla ilgili ilk öğretici, Azure portal hazırlamayı içerir.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 7042d3ede872c5dc854675334b964430671d1306
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065676"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-mini-r"></a>Öğretici: Azure Stack Edge Mini R 'yi dağıtmaya hazırlanma

Bu öğretici, Azure Stack Edge Mini R cihazının tamamen dağıtılması için gereken dağıtım öğreticilerinde ilkidir. Bu öğreticide, Azure portal Azure Stack Edge kaynağını dağıtmaya yönelik nasıl hazırlanılacağı açıklanmaktadır.

Kurulum ve yapılandırma işlemini tamamlamak için yönetici ayrıcalıkları gerekir. Portal hazırlığı 10 dakikadan kısa sürer.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yeni kaynak oluşturma
> * Etkinleştirme anahtarı alma

### <a name="get-started"></a>başlarken

Azure Stack Edge Mini R dağıtmak için, önceden tanımlanmış sırada aşağıdaki öğreticilere bakın.

| Adım | Description |
| --- | --- |
| **Hazırlama** |Bu adımların, yaklaşan dağıtım için hazırlanmasından tamamlanması gerekir. |
| **[Dağıtım yapılandırma denetim listesi](#deployment-configuration-checklist)** |Dağıtımdan önce ve dağıtım sırasında bilgi toplamak ve bilgileri kaydetmek için bu denetim listesini kullanın. |
| **[Dağıtım önkoşulları](#prerequisites)** |Bu Önkoşullar, ortamın dağıtım için hazırlandığını doğrular. |
|  | |
|**Dağıtım öğreticileri** |Azure Stack Edge Mini R cihazınızı üretime dağıtmak için bu öğreticiler gereklidir. |
|**[1. cihaz için Azure portal hazırlayın](azure-stack-edge-mini-r-deploy-prep.md)** |Fiziksel cihazı yüklemeden önce Azure Stack Edge kaynağını oluşturun ve yapılandırın. |
|**[2. cihazı yüklemesi](azure-stack-edge-mini-r-deploy-install.md)**|Fiziksel cihazınızı inceleyin ve kablosunu çıkarın.  |
|**[3. cihaza bağlanın](azure-stack-edge-mini-r-deploy-connect.md)** |Cihaz yüklendikten sonra, cihaz yerel Web Kullanıcı arabirimine bağlanın.  |
|**[4. ağ ayarlarını yapılandırma](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)** |Cihazınızın işlem ağı ve Web proxy ayarları dahil olmak üzere ağı yapılandırın.   |
|**[5. cihaz ayarlarını yapılandırma](azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)** |Bir cihaz adı ve DNS etki alanı atayın, güncelleştirme sunucusunu ve cihaz zamanını yapılandırın. |
|**[6. güvenlik ayarlarını yapılandırma](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)** |Kendi sertifikalarınızı kullanarak sertifikaları yapılandırın, VPN ayarlayın ve cihazınız için bekleyen şifrelemeyi yapılandırın.   |
|**[7. cihazı etkinleştirin](azure-stack-edge-mini-r-deploy-activate.md)** |Cihazı etkinleştirmek için hizmetten etkinleştirme anahtarını kullanın. Cihaz, SMB veya NFS paylaşımlarını ayarlamaya veya REST aracılığıyla bağlamaya hazırlanıyor. |
|**[8. işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md)** |Cihazınızda işlem rolünü yapılandırın. Bir Kubernetes kümesi de oluşturulur. |

Artık Azure portalını ayarlamaya başlayabilirsiniz.

## <a name="deployment-configuration-checklist"></a>Dağıtım yapılandırma denetim listesi

Cihazınızı dağıtmadan önce, Azure Stack Edge Mini R cihazınızda yazılımı yapılandırmak için bilgi toplamanız gerekir. Bu bilgilerin bir bölümünü önceden hazırlamak, cihazı ortamınızda dağıtma işlemini kolaylaştırmaya yardımcı olur. Cihazınızı dağıtırken yapılandırma ayrıntılarını bir yere dönmek için [Azure Stack Edge Mini dağıtım yapılandırması denetim listesini](azure-stack-edge-mini-r-deploy-checklist.md) kullanın.

## <a name="prerequisites"></a>Önkoşullar

Azure Stack Edge kaynağınız, Azure Stack Edge cihazınız ve veri merkezi ağı için yapılandırma önkoşulları aşağıda verilmiştir.

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge kaynağı için

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Azure Stack Edge cihazı için

Fiziksel cihazı dağıtmadan önce şunlardan emin olun:

- Bu cihaz için güvenlik bilgilerini [Azure Stack Edge cihazınız Için güvenlik yönergeleri](azure-stack-edge-mini-r-safety.md)' nde gözden geçirdiniz.
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>Veri merkezi ağı için

Başlamadan önce aşağıdakilerden emin olun:

- Veri merkezinizdeki ağ, Azure Stack Edge cihazınız için ağ gereksinimlerine göre yapılandırılır. Daha fazla bilgi için bkz. [Edge Mini R sistem gereksinimleri Azure Stack](azure-stack-edge-mini-r-system-requirements.md).

- Azure Stack Kenarlarınızın normal işletim koşullarında şunları yapabilirsiniz:

    - Cihazın güncel kalmasını sağlamak için en az 10 Mbps indirme bant genişliği.
    - Dosyaları aktarmak için en az 20 Mbps adanmış karşıya yükleme ve indirme bant genişliği.

## <a name="create-a-new-resource"></a>Yeni kaynak oluşturma

Fiziksel cihazınızı yönetmek için mevcut bir Azure Stack Edge kaynağınız varsa, bu adımı atlayın ve [etkinleştirme anahtarını almak](#get-the-activation-key)için gidin.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Azure Stack Edge kaynağı oluşturmak için Azure portal aşağıdaki adımları uygulayın.

1. Bu URL 'de Azure portal oturum açmak için Microsoft Azure kimlik bilgilerinizi kullanın: [https://portal.azure.com](https://portal.azure.com) .


2. Soldaki bölmede **+ Kaynak oluştur**’u seçin. **Azure Stack Edge/Data Box Gateway** için arama yapın ve seçin. **Oluştur**’u seçin. 

3. Azure Stack Edge Pro cihazı için kullanmak istediğiniz aboneliği seçin. Bu fiziksel cihazı göndermek istediğiniz ülkeyi seçin. **Cihazları göster**' i seçin.

    ![Kaynak oluşturma 1](media/azure-stack-edge-mini-r-deploy-prep/create-resource-1.png)


4. Cihaz türü seçin. **Azure Stack Edge** altında **Azure Stack Edge Mini R** ' yi ve ardından **Seç**' i seçin. Herhangi bir sorun görürseniz veya cihaz türünü seçmezseniz, [sorun giderme sorunları gider](azure-stack-edge-troubleshoot-ordering.md)bölümüne gidin.

    [![Kaynak oluştur 2](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png#lightbox)


5. **Temel bilgiler** sekmesinde, aşağıdaki **proje ayrıntılarını** girin veya seçin.
    
    |Ayar  |Değer  |
    |---------|---------|
    |Abonelik    |Abonelik, önceki seçime göre otomatik olarak doldurulur. Abonelik fatura hesabınıza bağlıdır. |
    |Kaynak grubu  |Mevcut grubu seçin veya yeni bir grup oluşturun.<br>[Azure Kaynak Grupları](../azure-resource-manager/management/overview.md) hakkında daha fazla bilgi edinin.     |


6. Aşağıdaki **örnek ayrıntılarını** girin veya seçin.

    |Ayar  |Değer  |
    |---------|---------|
    |Ad   | Kaynağı tanımlamak için kolay bir ad.<br>Ad, harf, sayı ve kısa çizgi içeren 2 ile 50 karakter arasında bir addır.<br> Ad bir harf veya rakamla başlar ve biter.        |
    |Region     |Azure Stack Edge kaynağının kullanılabildiği tüm bölgelerin listesi için bkz. [bölgeye göre kullanılabilir Azure ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Kamu kullanıyorsanız, tüm kamu bölgeleri [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/regions/)gösterildiği gibi kullanılabilir.<br> Cihazınızı dağıtmak istediğiniz coğrafi bölgeye yakın bir konum seçin.|

    ![Kaynak oluşturma 4](media/azure-stack-edge-mini-r-deploy-prep/create-resource-4.png)


7. **İleri ' yi seçin: sevkiyat adresi**.

   - Zaten bir cihazınız varsa, **zaten bir cihazım varsa** Birleşik giriş kutusunu seçin.

     ![Kaynak oluştur 5](media/azure-stack-edge-mini-r-deploy-prep/create-resource-5.png)

   - Bu, sipariş ettiğiniz yeni bir cihaz ise, ilgili kişi adını, şirketi, cihazı teslim etmek için adresi ve iletişim bilgilerini girin.

     ![Kaynak oluştur 6](media/azure-stack-edge-mini-r-deploy-prep/create-resource-6.png)

8. **Sonraki: Etiketler**' i seçin. İsteğe bağlı olarak kaynakları kategorilere ayırarak ve faturalandırmayı birleştirmek için Etiketler sağlayın. **Sonraki: Gözden geçirme ve oluşturma**’yı seçin.

9. **Gözden geçir + oluştur** sekmesinde, **fiyatlandırma ayrıntılarını**, **kullanım koşulları** ve kaynağınızın ayrıntılarını gözden geçirin. **Gizlilik koşullarını Incelediğim** Birleşik giriş kutusunu seçin.

    ![Kaynak oluşturma 7](media/azure-stack-edge-mini-r-deploy-prep/create-resource-7.png)

    Ayrıca, kaynak oluşturma sırasında, bulut hizmetlerinde kimlik doğrulaması yapmanızı sağlayan bir Yönetilen Hizmet Kimliği (MSI) etkin olduğunu da size bildirilir. Bu kimlik, kaynak mevcut olduğu sürece için mevcut.

10. **Oluştur**’u seçin.

    Kaynağın oluşturulması birkaç dakika sürer. Azure Stack Edge cihazının Azure 'da kaynak sağlayıcısıyla iletişim kurmasına imkan tanıyan bir MSI de oluşturulur.
    
    Kaynak başarıyla oluşturulup dağıtıldıktan sonra bilgilendirirsiniz. **Kaynağa git**’i seçin.
    
    ![Azure Stack Edge Pro kaynağına git](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-1.png)
    
Sipariş yerleştirildikten sonra, Microsoft siparişi inceler ve gönderim ayrıntıları ile size (e-posta aracılığıyla) ulaşır.

> [!NOTE]
> Aynı anda birden çok sipariş oluşturmak veya var olan bir siparişi kopyalamak istiyorsanız, [Azure örnekleri içindeki betikleri](https://github.com/Azure-Samples/azure-stack-edge-order)kullanabilirsiniz. Daha fazla bilgi için bkz. README dosyası.

Sipariş işlemi sırasında herhangi bir sorunla karşılaşırsanız, bkz. [sorun giderme sorunları giderme](azure-stack-edge-troubleshoot-ordering.md).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gerekirse, ortamınızı Azure CLı için hazırlayın.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure Stack Edge kaynağı oluşturmak için Azure CLı 'de aşağıdaki komutları çalıştırın.

1. [Az Group Create](/cli/azure/group#az_group_create) komutunu kullanarak bir kaynak grubu oluşturun veya var olan bir kaynak grubunu kullanın:

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Bir cihaz oluşturmak için [az databoxedge Device Create](/cli/azure/databoxedge/device#az_databoxedge_device_create) komutunu kullanın:

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgeMR_Mini
   ```

   Cihazınızı dağıtmak istediğiniz coğrafi bölgeye yakın bir konum seçin. Bölge yalnızca cihaz yönetimi için meta verileri depolar. Gerçek veriler herhangi bir depolama hesabında depolanabilir.

   Azure Stack Edge kaynağının kullanılabildiği tüm bölgelerin listesi için bkz. [bölgeye göre kullanılabilir Azure ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Kamu kullanıyorsanız, tüm kamu bölgeleri [Azure bölgelerinde](https://azure.microsoft.com/global-infrastructure/regions/)gösterildiği gibi kullanılabilir.

1. Sipariş oluşturmak için [az databoxedge Order Create](/cli/azure/databoxedge/order#az_databoxedge_order_create) komutunu çalıştırın:

   ```azurecli
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

Kaynağın oluşturulması birkaç dakika sürer. Sıralamayı görmek için [az databoxedge Order Show](/cli/azure/databoxedge/order#az_databoxedge_order_show) komutunu çalıştırın:

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

Sipariş verdikten sonra, Microsoft siparişi gözden geçirir ve teslim ayrıntıları ile e-posta ile sizinle iletişim kurar.

---

## <a name="get-the-activation-key"></a>Etkinleştirme anahtarı alma

Azure Stack Edge kaynağı çalışır duruma geçtikten sonra etkinleştirme anahtarını almanız gerekir. Bu anahtar Azure Stack Edge Mini R cihazınızı etkinleştirmek ve kaynakla bağlamak için kullanılır. Bu anahtarı şimdi, Azure portalındayken alabilirsiniz.

1. Oluşturduğunuz kaynağı seçin ve **genel bakış**' ı seçin.

   ![Cihaz kurulumunu seçin](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-2.png)

2. **Etkinleştir** kutucuğunda Azure Key Vault için bir ad girin veya varsayılan adı kabul edin. Anahtar Kasası adı 3 ila 24 karakter arasında olabilir. 

    Cihazınızla etkinleştirilen her Azure Stack Edge kaynağı için bir Anahtar Kasası oluşturulur. Anahtar Kasası, gizli dizileri depolamanıza ve erişmenize olanak tanır. Örneğin, hizmetin kanal bütünlüğü anahtarı (CIK) anahtar kasasında depolanır.

    Anahtar Kasası adı belirttikten sonra etkinleştirme anahtarı oluşturmak için **etkinleştirme anahtarı oluştur** ' u seçin.

    [![Etkinleştirme anahtarını alma](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png)](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png#lightbox)

    Anahtar Kasası ve etkinleştirme anahtarı oluşturulurken birkaç dakika bekleyin. Anahtarı kopyalamak için Kopyala simgesini seçin ve daha sonra kullanmak üzere kaydedin.

> [!IMPORTANT]
> - Etkinleştirme anahtarı üretilmeden üç gün sonra dolar.
> - Anahtarın süresi dolmuşsa yeni bir anahtar oluşturun. Eski anahtar geçerli olmaz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki gibi Azure Stack Edge konuları hakkında bilgi edindiniz:

> [!div class="checklist"]
> * Yeni kaynak oluşturma
> * Etkinleştirme anahtarı alma

Azure Stack Edge 'i nasıl yükleyeceğinizi öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Azure Stack Edge 'i yükler](./azure-stack-edge-mini-r-deploy-install.md)
