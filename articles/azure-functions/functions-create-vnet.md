---
title: Azure Işlevlerini Azure sanal ağıyla tümleştirme
description: Bir Azure sanal ağına bir işlev bağlamayı gösteren adım adım öğretici
author: alexkarcher-msft
manager: gwallace
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: bc6c87a28078d25a212a681206258d6d369f2867
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575536"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Öğretici: Işlevleri bir Azure sanal ağı ile tümleştirme

Bu öğreticide, Azure sanal ağındaki kaynaklara bağlanmak için Azure Işlevlerinin nasıl kullanılacağı gösterilmektedir. hem İnternet hem de sanal ağda WordPress çalıştıran bir VM 'ye erişimi olan bir işlev oluşturacaksınız.

> [!div class="checklist"]
> * Premium planda bir işlev uygulaması oluşturma
> * Sanal ağdaki VM 'ye bir WordPress sitesi dağıtma
> * İşlev uygulamasını sanal ağa bağlama
> * WordPress kaynaklarına erişmek için bir işlev ara sunucusu oluşturma
> * Sanal ağın içinden bir WordPress dosyası iste

## <a name="topology"></a>Topoloji

Aşağıdaki diyagramda, oluşturduğunuz çözümün mimarisi gösterilmektedir:

 ![Sanal Ağ tümleştirmesi için Kullanıcı arabirimi](./media/functions-create-vnet/topology.png)

Premium planda çalışan işlevler, VNet tümleştirme özelliği de dahil olmak üzere Azure App Service Web Apps ile aynı barındırma özelliklerine sahiptir. Sorun giderme ve gelişmiş yapılandırma dahil VNet tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [uygulamanızı bir Azure sanal ağı Ile tümleştirme](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticide IP adresleme ve alt ağ oluşturma hakkında bilgi almanız önemlidir. [Bu makaleyle, adresleme ve alt ağ oluşturma temellerini kapsayan bir](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)başlangıç yapabilirsiniz. Daha birçok makale ve video çevrimiçi olarak kullanılabilir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-function-app-in-a-premium-plan"></a>Premium planda bir işlev uygulaması oluşturma

İlk olarak, [Premium plan]bir işlev uygulaması oluşturursunuz. Bu plan, sanal ağ tümleştirmesini desteklerken sunucusuz ölçek sağlar.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Sağ üst köşedeki sabitleme simgesini seçerek işlev uygulamasını panoya sabitleyebilirsiniz. Sabitleme, VM 'nizi oluşturduktan sonra bu işlev uygulamasına döndürülmesini kolaylaştırır.

## <a name="create-a-vm-inside-a-virtual-network"></a>Sanal ağ içinde VM oluşturma

Daha sonra, bir sanal ağ içinde WordPress çalıştıran önceden yapılandırılmış bir VM oluşturun (bkz., Jetware tarafından[WordPress LEMP7 maksimum performansı](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) ). Düşük maliyetli ve rahatlığı nedeniyle bir WordPress sanal makinesi kullanılır. Aynı senaryo, bir sanal ağdaki REST API 'Leri, App Service ortamları ve diğer Azure hizmetleri gibi tüm kaynaklar ile birlikte kullanılabilir. 

1. Portalda, sol gezinti bölmesinde **+ kaynak oluştur** ' u seçin, arama alanı türü `WordPress LEMP7 Max Performance`yazın ve ENTER tuşuna basın.

1. Arama sonuçlarında **WordPress LEMP Max performansı** ' nı seçin. **Yazılım planı** olarak **CentOS IÇIN WordPress Permp maksimum performansının** yazılım planını seçin ve **Oluştur**' u seçin.

1. **Temel bilgiler** sekmesinde, görüntünün altındaki tabloda belirtilen VM ayarlarını kullanın:

    ![VM oluşturmak için temel bilgiler sekmesi](./media/functions-create-vnet/create-vm-1.png)

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Abonelik** | Aboneliğiniz | Kaynaklarınızın oluşturulduğu abonelik. | 
    | **[Kaynak grubu](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | `myResourceGroup`veya işlev uygulamanız ile oluşturduğunuz kaynak grubunu seçin. İşlev uygulaması, WordPress VM ve barındırma planı için aynı kaynak grubunu kullanmak, bu öğreticiyle işiniz bittiğinde kaynakları temizlemeyi kolaylaştırır. |
    | **Sanal makine adı** | VNET-WordPress | VM adının kaynak grubunda benzersiz olması gerekir |
    | **[Geli](https://azure.microsoft.com/regions/)** | (Avrupa) Batı Avrupa | Size yakın veya sanal makineye erişen işlevlerin yakınında bir bölge seçin. |
    | **Boyut** | B1S | **Boyutu Değiştir** ' i seçin ve ardından 1 vCPU ve 1 GB belleği olan B1S standart görüntüsünü seçin. |
    | **Kimlik doğrulama türü** | Parola | Parola kimlik doğrulamasını kullanmak için bir **Kullanıcı adı**, güvenli bir **parola**belirtmeniz ve ardından **parolayı onaylamanız**gerekir. Bu öğreticide, sorun gidermenize gerek olmadığı takdirde VM 'de oturum açmanız gerekmez. |

1. **Ağ** sekmesini seçin ve sanal ağları Yapılandır altında **Yeni oluştur**' u seçin.

1. **Sanal ağ oluştur**' da, görüntünün altındaki tabloda bulunan ayarları kullanın:

    ![VM oluşturma Ağ sekmesi](./media/functions-create-vnet/create-vm-2.png)

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Ad** | MyResourceGroup-VNET | Sanal ağınız için oluşturulan varsayılan adı kullanabilirsiniz. |
    | **Adres aralığı** | 10.10.0.0/16 | Sanal ağ için tek bir adres aralığı kullanın. |
    | **Alt ağ adı** | Öğretici-net | Alt ağın adı. |
    | **Adres aralığı** (alt ağ) | 10.10.1.0/24   | Alt ağ boyutu, alt ağa kaç arabirim eklenebileceğini tanımlar. Bu alt ağ WordPress sitesi tarafından kullanılır.  Bir `/24` alt ağı 254 ana bilgisayar adresi sağlar. |

1. Sanal ağı oluşturmak için **Tamam ' ı** seçin.

1. **Ağ** sekmesine geri döndüğünüzde **genel IP**için **yok** ' u seçin.

1. **Yönetim** sekmesini seçin ve ardından **Tanılama depolama hesabı**' nda, işlev uygulamanızla oluşturduğunuz depolama hesabını seçin.

1. **İncele ve oluştur**’u seçin. Doğrulama tamamlandıktan sonra **Oluştur**' u seçin. VM oluşturma işlemi birkaç dakika sürer. Oluşturulan VM yalnızca sanal ağa erişebilir.

1. VM oluşturulduktan sonra, yeni VM 'nizin sayfasını görüntülemek için **Kaynağa Git** ' i seçin ve ardından **Ayarlar**altında **ağ** ' ı seçin.

1. **Genel IP**bulunmadığından emin olun. İşlev uygulamanızdan sanal makineye bağlanmak için kullandığınız **özel IP**'yi bir yere unutmayın.

    ![VM 'deki ağ ayarları](./media/functions-create-vnet/vm-networking.png)

Artık sanal ağınız içinde tamamen dağıtılan bir WordPress siteniz var. Bu siteye genel İnternet 'ten erişilemiyor.

## <a name="connect-your-function-app-to-the-virtual-network"></a>İşlev uygulamanızı sanal ağa bağlama

Bir sanal ağdaki VM 'de çalışan bir WordPress sitesi ile artık işlev uygulamanızı bu sanal ağa bağlayabilirsiniz.

1. Yeni işlev uygulamanızda, **ağ** > **platform özellikleri** ' ni seçin.

    ![İşlev uygulamasındaki ağı seçin](./media/functions-create-vnet/networking-0.png)

1. **VNET tümleştirmesi**altında, **yapılandırmak Için buraya tıklayın ' ı**seçin.

    ![Ağ özelliği yapılandırma durumu](./media/functions-create-vnet/Networking-1.png)

1. Sanal ağ tümleştirme sayfasında **VNET Ekle (Önizleme)** öğesini seçin.

    ![VNet tümleştirme önizlemesini ekleyin](./media/functions-create-vnet/networking-2.png)

1. **Ağ özelliği durumu**' nda, görüntünün altındaki tabloda bulunan ayarları kullanın:

    ![App Virtual Network işlevini tanımlayın](./media/functions-create-vnet/networking-3.png)

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Sanal Ağ** | MyResourceGroup-VNET | Bu sanal ağ, daha önce oluşturduğunuz bir tane. |
    | **Alt ağ** | Yeni alt ağ oluştur | İşlev uygulamanızın kullanması için sanal ağda bir alt ağ oluşturun. VNet tümleştirmesi boş bir alt ağ kullanacak şekilde yapılandırılmalıdır. İşlevlerinizin sanal makinenizin dışında farklı bir alt ağ kullanması fark etmez. Sanal ağ iki alt ağ arasındaki trafiği otomatik olarak yönlendirir. |
    | **Alt ağ adı** | İşlev-net | Yeni alt ağın adı. |
    | **Sanal ağ adres bloğu** | 10.10.0.0/16 | WordPress sitesi tarafından kullanılan aynı adres bloğunu seçin. Yalnızca bir adres bloğu tanımlanmış olmalıdır. |
    | **Adres aralığı** | 10.10.2.0/24   | Alt ağ boyutu, Premium plan işlev uygulamanızın ölçeklenebilmesini sağlayan toplam örnek sayısını kısıtlar. Bu örnek, 254 kullanılabilir ana bilgisayar adresiyle bir `/24` alt ağı kullanır. Bu alt ağ daha fazla sağlanmış, ancak hesaplama kolaydır. |

1. Alt ağı eklemek için **Tamam ' ı** seçin. İşlev uygulaması sayfanıza dönmek için VNet tümleştirme ve ağ özelliği durum sayfalarını kapatın.

İşlev uygulaması artık WordPress sitesinin çalıştığı sanal ağa erişebilir. Ardından, WordPress sitesinden bir dosya döndürmek için [Azure işlev proxy'leri](functions-proxies.md) kullanırsınız.

## <a name="create-a-proxy-to-access-vm-resources"></a>VM kaynaklarına erişmek için bir proxy oluşturma

VNet tümleştirmesi etkinken, sanal ağda çalışan VM 'ye istekleri iletmek için işlev uygulamanızda bir ara sunucu oluşturabilirsiniz.

1. İşlev uygulamanızda **proxy** >  **+** seçin ve ardından görüntünün altındaki tabloda bulunan proxy ayarlarını kullanın:

    ![Proxy ayarlarını tanımlama](./media/functions-create-vnet/create-proxy.png)

    | Ayar  | Önerilen değer  | Açıklama      |
    | -------- | ---------------- | ---------------- |
    | **Ad** | Bit | Ad herhangi bir değer olabilir. Proxy 'yi tanımlamak için kullanılır. |
    | **Rota şablonu** | /bitki | Bir VM kaynağıyla eşleşen rota. |
    | **Arka uç URL 'SI** | http://< YOUR_VM_IP >/wp-content/themes/twentyseventeen/Assets/images/header.jpg | `<YOUR_VM_IP>`, daha önce oluşturduğunuz WordPress sanal makinenizin IP adresi ile değiştirin. Bu eşleme, siteden tek bir dosya döndürüyor. |

1. Proxy 'yi işlev uygulamanıza eklemek için **Oluştur** ' u seçin.

## <a name="try-it-out"></a>Deneyin

1. Tarayıcınızda, **arka uç URL 'si**olarak kullandığınız URL 'ye erişmeyi deneyin. Beklendiği gibi, istek zaman aşımına uğrar. WordPress siteniz internet 'e değil yalnızca sanal ağınıza bağlı olduğundan zaman aşımı oluşur.

1. **Proxy URL 'si** değerini yeni proxy 'nizden kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. Döndürülen görüntü, sanal ağınızın içinde çalışan WordPress sitesinden yapılır.

    ![WordPress sitesinden döndürülen tesis görüntü dosyası](./media/functions-create-vnet/plant.png)

İşlev uygulamanız hem İnternet hem de sanal ağınıza bağlı. Proxy, genel İnternet üzerinden bir istek alıyor ve sonra bu isteği bağlı sanal ağa iletmek için basit bir HTTP proxy 'si olarak hareket etmektedir. Daha sonra proxy, yanıtı internet üzerinden herkese açık bir şekilde geçirir.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, WordPress sitesi, işlev uygulamasında bir proxy kullanılarak çağrılan bir API işlevi görür. Bu senaryo, kolayca ayarlanması ve görselleştirilmesi için iyi bir öğretici sağlar. Bir sanal ağ içinde dağıtılan diğer API 'leri kullanabilirsiniz. Ayrıca, sanal ağ içinde dağıtılan API 'Leri çağıran kodla bir işlev oluşturmuş olabilirsiniz. Daha gerçekçi bir senaryo, sanal ağda dağıtılan bir SQL Server örneğini çağırmak için veri istemcisi API 'Lerini kullanan bir işlevdir.

Premium planda çalışan işlevler, PremiumV2 planlarındaki Web Apps ile aynı temel App Service altyapısını paylaşır. [Azure App Service içindeki Web uygulamalarına](../app-service/overview.md) yönelik tüm belgeler Premium plan işlevleriniz için geçerlidir.

> [!div class="nextstepaction"]
> [Işlevlerde ağ seçenekleri hakkında daha fazla bilgi edinin](./functions-networking-options.md)

[Premium plan]: functions-scale.md#premium-plan
