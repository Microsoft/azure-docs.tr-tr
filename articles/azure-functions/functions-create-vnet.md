---
title: Azure Işlevlerini bir sanal ağla bütünleştirmek için özel uç noktaları kullanın
description: Bu öğreticide bir Azure sanal ağına nasıl bir işlev bağlanacağı ve özel uç noktalar kullanarak nasıl kilitleneceği gösterilmektedir.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: e1ed944250f05f52860c47f6cb61130f50b08e7c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078783"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>Öğretici: özel uç noktaları kullanarak Azure Işlevlerini bir Azure sanal ağı ile tümleştirme

Bu öğreticide, bir Azure sanal ağındaki kaynaklara özel uç noktalar kullanarak bağlanmak için Azure Işlevlerinin nasıl kullanılacağı gösterilmektedir. Bir sanal ağın arkasında kilitlenen bir depolama hesabı kullanarak bir işlev oluşturacaksınız. Sanal ağ bir Service Bus kuyruğu tetikleyicisi kullanır.

Bu öğreticide şunları yapmanız gerekir:

> [!div class="checklist"]
> * Premium planda bir işlev uygulaması oluşturun.
> * Hizmet veri yolu, depolama hesabı ve sanal ağ gibi Azure kaynakları oluşturun.
> * Depolama hesabınızı özel bir uç noktanın arkasında kilitleyin.
> * Özel bir uç noktanın arkasındaki Service Bus 'ı kilitleyin.
> * Hem Service Bus hem de HTTP tetikleyicilerini kullanan bir işlev uygulaması dağıtın.
> * İşlev uygulamanızı özel bir uç noktanın arkasında kilitleyin.
> * İşlev uygulamanızın sanal ağ içinde güvenli olduğunu görmek için test edin.
> * Kaynakları temizleyin.

## <a name="create-a-function-app-in-a-premium-plan"></a>Premium planda bir işlev uygulaması oluşturma

Bu öğretici C# kullandığından Premium planda bir .NET işlevi uygulaması oluşturacaksınız. Diğer diller de Windows 'da desteklenmektedir. Premium planı, sanal ağ tümleştirmesini desteklerken sunucusuz ölçek sağlar.

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.

1. **Yeni** sayfasında, **işlem**  >  **işlev uygulaması**' yi seçin.

1. **Temel bilgiler** sayfasında, işlev uygulaması ayarlarını yapılandırmak için aşağıdaki tabloyu kullanın.

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **Abonelik** | Aboneliğiniz | Bu yeni işlev uygulamasının oluşturulduğu abonelik. |
    | **[Kaynak Grubu](../azure-resource-manager/management/overview.md)** |  myResourceGroup | İşlev uygulamanızı oluşturacağınız yeni kaynak grubunun adı. |
    | **İşlev Uygulamasının adı** | Genel olarak benzersiz bir ad | Yeni işlev uygulamanızı tanımlayan ad. Geçerli karakterler şunlardır: `a-z` (büyük/küçük harf duyarsız), `0-9` ve `-`.  |
    |**Yayımla**| Kod | Kod dosyalarını veya bir Docker kapsayıcısını yayımlamayı seçin. |
    | **Çalışma zamanı yığını** | .NET | Bu öğretici .NET kullanır. |
    |**Bölge**| Tercih edilen bölge | Size yakın bir [bölge](https://azure.microsoft.com/regions/) seçin ve işlevlerinizin erişebileceği diğer hizmetleri yakın bir şekilde seçin. |

1. Ileri 'yi seçin **: barındırma**. **Barındırma** sayfasında, aşağıdaki ayarları girin.

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **[Depolama hesabı](../storage/common/storage-account-create.md)** |  Genel olarak benzersiz bir ad |  İşlev uygulamanız tarafından kullanılan bir depolama hesabı oluşturun. Depolama hesabı adları 3 ila 24 karakter uzunluğunda olmalıdır. Yalnızca sayılar ve küçük harfler içerebilir. Ayrıca, [depolama hesabı gereksinimlerini](./storage-considerations.md#storage-account-requirements)karşılaması gereken mevcut bir hesabı da kullanabilirsiniz. |
    |**İşletim sistemi**| Windows | Bu öğretici Windows kullanır. |
    | **[Planlama](./functions-scale.md)** | Premium | Kaynakların işlev uygulamanıza nasıl ayrılacağını tanımlayan barındırma planı. Varsayılan olarak, **Premium**' u seçtiğinizde yeni bir App Service planı oluşturulur. Varsayılan **SKU ve boyut** **EP1**, burada *EP* , _elastik Premium_ için temsil eder. Daha fazla bilgi için bkz. [Premium SKU](./functions-premium-plan.md#available-instance-skus)'ların listesi.<br/><br/>Premium bir planda JavaScript işlevleri çalıştırdığınızda, daha az vCPU içeren bir örnek seçin. Daha fazla bilgi için bkz. [tek çekirdekli Premium planları seçme](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. **İleri: izleme** öğesini seçin. **İzleme** sayfasında, aşağıdaki ayarları girin.

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Varsayılan | En yakın desteklenen bölgede aynı uygulama adında bir Application Insights kaynağı oluşturun. **Yeni kaynak adını** değiştirmeniz veya verilerinizi bir [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/)'da farklı bir **konuma** depolamanız gerekirse bu ayarı genişletin. |

1. Uygulama yapılandırma seçimlerini gözden geçirmek için **gözden geçir + oluştur** ' u seçin.

1. **Gözden geçir + oluştur** sayfasında ayarlarınızı gözden geçirin. Ardından, işlev uygulamasını sağlamak ve dağıtmak için **Oluştur** ' u seçin.

1. Portalın sağ üst köşesinde **Bildirimler** simgesini seçin ve **dağıtım başarılı** iletisini izleyin.

1. Yeni işlev uygulamanızı görüntülemek için **Kaynağa git**’i seçin. **Panoya sabitle ' yi** de seçebilirsiniz. Sabitleme, panonuzdan bu işlev uygulama kaynağına döndürülmesini kolaylaştırır.

Tebrikler! Premium işlev uygulamanızı başarıyla oluşturdunuz.

## <a name="create-azure-resources"></a>Azure kaynakları oluşturma

Ardından, bir depolama hesabı, bir hizmet veri yolu ve bir sanal ağ oluşturacaksınız. 
### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Sanal ağlarınızda, işlev uygulamanız ile oluşturduğunuz bir depolama hesabı gerekir.

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.

1. **Yeni** sayfada *depolama hesabı*' nı arayın. Ardından **Oluştur**’u seçin.

1. **Temel bilgiler** sekmesinde, depolama hesabı ayarlarını yapılandırmak için aşağıdaki tabloyu kullanın. Diğer tüm ayarlar varsayılan değerleri kullanabilir.

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Abonelik** | Aboneliğiniz | Kaynaklarınızın oluşturulduğu abonelik. | 
    | **[Kaynak grubu](../azure-resource-manager/management/overview.md)**  | myResourceGroup | İşlev uygulamanız ile oluşturduğunuz kaynak grubu. |
    | **Ad** | mysecurestorage| Özel uç noktanın uygulanacağı depolama hesabının adı. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | İşlev uygulamanızı oluşturduğunuz bölge. |

1. **Gözden geçir ve oluştur**’u seçin. Doğrulama bittikten sonra **Oluştur**' u seçin.

### <a name="create-a-service-bus"></a>Service Bus oluşturma

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.

1. **Yeni** sayfada *Service Bus*' ı arayın. Ardından **Oluştur**’u seçin.

1. **Temel bilgiler** sekmesinde, Service Bus ayarlarını yapılandırmak için aşağıdaki tabloyu kullanın. Diğer tüm ayarlar varsayılan değerleri kullanabilir.

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Abonelik** | Aboneliğiniz | Kaynaklarınızın oluşturulduğu abonelik. |
    | **[Kaynak grubu](../azure-resource-manager/management/overview.md)**  | myResourceGroup | İşlev uygulamanız ile oluşturduğunuz kaynak grubu. |
    | **Ad** | myServiceBus| Özel uç noktanın uygulanacağı hizmet veri yolu adı. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | İşlev uygulamanızı oluşturduğunuz bölge. |
    | **Fiyatlandırma katmanı** | Premium | Azure Service Bus ile özel uç noktaları kullanmak için bu katmanı seçin. |

1. **Gözden geçir ve oluştur**’u seçin. Doğrulama bittikten sonra **Oluştur**' u seçin.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Bu öğreticideki Azure kaynakları sanal bir ağa tümleştirilir veya bir sanal ağ içinde yer alır. Özel uç noktaları, sanal ağ içindeki ağ trafiğini içerecek şekilde kullanacaksınız.

Öğretici iki alt ağ oluşturur:
- **varsayılan**: özel uç noktalar için alt ağ. Özel IP adresleri bu alt ağdan verilir.
- **işlevler**: Azure işlevleri sanal ağ tümleştirmesi için alt ağ. Bu alt ağ, işlev uygulamasına atanmış.

İşlev uygulamasının tümleştirildiği sanal ağı oluşturun:

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.

1. **Yeni** sayfasında, *sanal ağ*' ı arayın. Ardından **Oluştur**’u seçin.

1. **Temel bilgiler** sekmesinde, sanal ağ ayarlarını yapılandırmak için aşağıdaki tabloyu kullanın.

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Abonelik** | Aboneliğiniz | Kaynaklarınızın oluşturulduğu abonelik. | 
    | **[Kaynak grubu](../azure-resource-manager/management/overview.md)**  | myResourceGroup | İşlev uygulamanız ile oluşturduğunuz kaynak grubu. |
    | **Ad** | myVirtualNet| İşlev uygulamanızın bağlanacağı sanal ağın adı. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | İşlev uygulamanızı oluşturduğunuz bölge. |

1. **IP adresleri** sekmesinde **alt ağ ekle**' yi seçin. Alt ağ ayarlarını yapılandırmak için aşağıdaki tabloyu kullanın.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Sanal ağ yapılandırması oluşturma görünümünün ekran görüntüsü.":::

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Alt ağ adı** |  işlevleri | İşlev uygulamanızın bağlanacağı alt ağın adı. | 
    | **Alt ağ adres aralığı** | 10.0.1.0/24 | Alt ağ adres aralığı. Önceki görüntüde, IPv4 adres alanının 10.0.0.0/16 olduğuna dikkat edin. Değer 10.1.0.0/16 ise, önerilen alt ağ adres aralığı 10.1.1.0/24 olur. |

1. **Gözden geçir ve oluştur**’u seçin. Doğrulama bittikten sonra **Oluştur**' u seçin.

## <a name="lock-down-your-storage-account"></a>Depolama hesabınızı kilitleme

Azure özel uç noktaları, belirli Azure kaynaklarına özel bir IP adresi kullanarak bağlanmak için kullanılır. Bu bağlantı, ağ trafiğinin seçili sanal ağ içinde kalmasını ve erişimin yalnızca belirli kaynaklar için kullanılabilir olmasını sağlar. 

Depolama hesabınızı kullanarak Azure dosyaları depolaması ve Azure Blob depolama için özel uç noktalar oluşturun:

1. Yeni depolama hesabınızda, sol taraftaki menüden **ağ**' ı seçin.

1. **Özel uç nokta bağlantıları** sekmesinde **Özel uç nokta**' ı seçin.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Depolama hesabı için özel bitiş noktaları oluşturma ekranının ekran görüntüsü.":::

1. **Temel bilgiler** sekmesinde, aşağıdaki tabloda gösterilen özel uç nokta ayarlarını kullanın.

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Abonelik** | Aboneliğiniz | Kaynaklarınızın oluşturulduğu abonelik. | 
    | **[Kaynak grubu](../azure-resource-manager/management/overview.md)**  | myResourceGroup | İşlev uygulamanız ile oluşturduğunuz kaynak grubunu seçin. |
    | **Ad** | dosya-uç noktası | Depolama hesabınızdaki dosyaların özel uç noktasının adı. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Depolama hesabınızı oluşturduğunuz bölgeyi seçin. |

1. **Kaynak** sekmesinde, aşağıdaki tabloda gösterilen özel uç nokta ayarlarını kullanın.

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Abonelik** | Aboneliğiniz | Kaynaklarınızın oluşturulduğu abonelik. | 
    | **Kaynak türü**  | Microsoft. Storage/storageAccounts | Depolama hesapları için kaynak türü. |
    | **Kaynak** | mysecurestorage | Oluşturduğunuz depolama hesabı. |
    | **Hedef alt kaynak** |  dosyası | Depolama hesabındaki dosyalar için kullanılacak özel uç nokta. |

1. **Yapılandırma** sekmesinde, **alt ağ** ayarı için **varsayılan**' ı seçin.

1. **Gözden geçir ve oluştur**’u seçin. Doğrulama bittikten sonra **Oluştur**' u seçin. Sanal ağdaki kaynaklar artık depolama dosyalarıyla iletişim kurabilir.

1. Bloblar için başka bir özel uç nokta oluşturun. **Kaynaklar** sekmesinde, aşağıdaki tabloda gösterilen ayarları kullanın. Diğer tüm ayarlar için, dosyalar için özel uç nokta oluşturmak için kullandığınız değerleri kullanın.

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Abonelik** | Aboneliğiniz | Kaynaklarınızın oluşturulduğu abonelik. | 
    | **Kaynak türü**  | Microsoft. Storage/storageAccounts | Depolama hesapları için kaynak türü. |
    | **Kaynak** | mysecurestorage | Oluşturduğunuz depolama hesabı. |
    | **Hedef alt kaynak** | blob | Depolama hesabından Bloblar için kullanılacak özel uç nokta. |

## <a name="lock-down-your-service-bus"></a>Service Bus 'ı kilitleme

Service Bus 'ı kilitlemek için özel uç nokta oluşturun:

1. Yeni hizmet veri yolunda sol taraftaki menüde **ağ**' ı seçin.

1. **Özel uç nokta bağlantıları** sekmesinde **Özel uç nokta**' ı seçin.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Service Bus için özel uç noktalara nasıl gidegösteren ekran görüntüsü.":::

1. **Temel bilgiler** sekmesinde, aşağıdaki tabloda gösterilen özel uç nokta ayarlarını kullanın.

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Abonelik** | Aboneliğiniz | Kaynaklarınızın oluşturulduğu abonelik. | 
    | **[Kaynak grubu](../azure-resource-manager/management/overview.md)**  | myResourceGroup | İşlev uygulamanız ile oluşturduğunuz kaynak grubu. |
    | **Ad** | SB-uç nokta | Depolama hesabınızdaki dosyaların özel uç noktasının adı. |
    | **[Region](https://azure.microsoft.com/regions/)** | myFunctionRegion | Depolama hesabınızı oluşturduğunuz bölge. |

1. **Kaynak** sekmesinde, aşağıdaki tabloda gösterilen özel uç nokta ayarlarını kullanın.

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Abonelik** | Aboneliğiniz | Kaynaklarınızın oluşturulduğu abonelik. | 
    | **Kaynak türü**  | Microsoft. ServiceBus/ad alanları | Service Bus için kaynak türü. |
    | **Kaynak** | myServiceBus | Öğreticide daha önce oluşturduğunuz hizmet veri yolu. |
    | **Hedef alt kaynak** | ad alanı | Hizmet veri yolundan ad alanı için kullanılacak özel uç nokta. |

1. **Yapılandırma** sekmesinde, **alt ağ** ayarı için **varsayılan**' ı seçin.

1. **Gözden geçir ve oluştur**’u seçin. Doğrulama bittikten sonra **Oluştur**' u seçin. 

Sanal ağdaki kaynaklar artık Service Bus ile iletişim kurabilir.

## <a name="create-a-file-share"></a>Dosya paylaşımı oluşturma

1. Oluşturduğunuz depolama hesabında, sol taraftaki menüde **dosya paylaşımları**' nı seçin.

1. **+ Dosya paylaşımları**' nı seçin. Bu öğreticinin amaçları doğrultusunda dosya paylaşma *dosyalarını* adlandırın.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Depolama hesabında bir dosya paylaşımının nasıl oluşturulacağı ekran görüntüsü.":::

1. **Oluştur**’u seçin.

## <a name="get-the-storage-account-connection-string"></a>Depolama hesabı bağlantı dizesini al

1. Oluşturduğunuz depolama hesabında, sol taraftaki menüden **erişim tuşları**' nı seçin.

1. **Anahtarları göster**' i seçin. **KEY1** bağlantı dizesini kopyalayın ve kaydedin. Uygulama ayarlarını yapılandırırken bu bağlantı dizesine ihtiyacınız olacaktır.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Depolama hesabı bağlantı dizesinin nasıl alınacağını gösteren ekran görüntüsü.":::

## <a name="create-a-queue"></a>Bir kuyruk oluşturma

Azure Işlevleri Service Bus tetikleyicinizin olay alacak olan kuyruğu oluşturun:

1. Service Bus 'da, sol taraftaki menüden **Kuyruklar**' ı seçin.

1. **Paylaşılan erişim ilkeleri**' ni seçin. Bu öğreticinin amaçları doğrultusunda, liste *kuyruğunu* adlandırın.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Service Bus kuyruğu oluşturma ekranının ekran görüntüsü.":::

1. **Oluştur**’u seçin.

## <a name="get-a-service-bus-connection-string"></a>Service Bus bağlantı dizesi al

1. Service Bus 'da sol taraftaki menüden **paylaşılan erişim ilkeleri**' ni seçin.

1. **RootManageSharedAccessKey** öğesini seçin. **Birincil bağlantı dizesini** kopyalayın ve kaydedin. Uygulama ayarlarını yapılandırırken bu bağlantı dizesine ihtiyacınız olacaktır.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Service Bus bağlantı dizesinin nasıl alınacağını gösteren ekran görüntüsü.":::

## <a name="integrate-the-function-app"></a>İşlev uygulamasını tümleştirme

İşlev uygulamanızı sanal ağlarla birlikte kullanmak için bir alt ağa katılmanız gerekir. Azure Işlevleri sanal ağ tümleştirmesi için belirli bir alt ağ kullanacaksınız. Bu öğreticide oluşturduğunuz diğer özel uç noktalar için varsayılan alt ağı kullanacaksınız.

1. İşlev uygulamanızda, sol taraftaki menüden **ağ**' ı seçin.

1. **VNET tümleştirmesi** altında, **yapılandırmak Için buraya tıklayın ' ı** seçin.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Sanal ağ tümleştirmesine nasıl gidegösteren ekran görüntüsü.":::

1. **VNET Ekle**' yi seçin.

1. **Sanal ağ** altında, daha önce oluşturduğunuz sanal ağı seçin.

1. Daha önce oluşturduğunuz **işlevler** alt ağını seçin. İşlev uygulamanız artık sanal ağınızla tümleşiktir!

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Bir işlev uygulamasının bir alt ağa nasıl bağlanacağını gösteren ekran görüntüsü.":::

## <a name="configure-your-function-app-settings"></a>İşlev uygulaması ayarlarınızı yapılandırın

1. İşlev uygulamanızda, sol taraftaki menüde **yapılandırma**' yı seçin.

1. İşlev uygulamanızı sanal ağlarla birlikte kullanmak için aşağıdaki tabloda gösterilen uygulama ayarlarını güncelleştirin. Bir ayarı eklemek veya düzenlemek için, uygulama ayarları tablosunun en sağdaki sütunundaki **+ Yeni uygulama ayarı** ' nı veya **Düzenle** simgesini seçin. Bitirdiğinizde **Kaydet**' i seçin.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Özel uç noktalar için işlev uygulama ayarlarının nasıl yapılandırılacağı ekran görüntüsü.":::

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | Oluşturduğunuz depolama hesabının bağlantı dizesi. Bu depolama bağlantı dizesi, [depolama hesabı bağlantı dizesi al](#get-the-storage-account-connection-string) bölümüne ait. Bu ayar, işlev uygulamanızın çalışma zamanında normal işlemler için güvenli depolama hesabı kullanmasına izin verir. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | Oluşturduğunuz depolama hesabının bağlantı dizesi. Bu ayar, işlev uygulamanızın dağıtım sırasında kullanılan Azure dosyaları için güvenli depolama hesabı kullanmasına izin verir. |
    | **WEBSITE_CONTENTSHARE** | files | Depolama hesabında oluşturduğunuz dosya paylaşımının adı. Bu ayarı WEBSITE_CONTENTAZUREFILECONNECTIONSTRING kullanın. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Service Bus 'ın bağlantı dizesi için bu uygulama ayarını oluşturun. Bu depolama bağlantı dizesi, [Service Bus bağlantı dizesi al](#get-a-service-bus-connection-string) bölümünden alınır.|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Bu uygulama ayarını oluştur. 1 değeri, depolama hesabınız bir sanal ağla sınırlı olduğunda işlev uygulamanızın ölçeğini ölçeklendirmesini sağlar. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Bu uygulama ayarını oluştur. Uygulamanız bir sanal ağla tümleştirirse, sanal ağ ile aynı DNS sunucusunu kullanacaktır. İşlev uygulamanızın Azure DNS özel bölgelerle çalışabilmesi için bu ayara ihtiyacı vardır. Özel uç noktaları kullandığınızda bu gereklidir. Bu ayar ve WEBSITE_VNET_ROUTE_ALL, uygulamanızdaki tüm giden çağrıları sanal ağınıza gönderir. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Bu uygulama ayarını oluştur. Uygulamanız bir sanal ağla tümleştirirse, sanal ağ ile aynı DNS sunucusunu kullanır. İşlev uygulamanızın Azure DNS özel bölgelerle çalışabilmesi için bu ayara ihtiyacı vardır. Özel uç noktaları kullandığınızda bu gereklidir. Bu ayar ve WEBSITE_DNS_SERVER, uygulamanızdaki tüm giden çağrıları sanal ağınıza gönderir. |

1. **Yapılandırma** görünümünde, **işlev çalışma zamanı ayarları** sekmesini seçin.

1. **Çalışma zamanı ölçek Izlemeyi** **Açık** olarak ayarlayın. Sonra **Kaydet**'i seçin. Çalışma zamanı tabanlı ölçeklendirme, HTTP olmayan tetikleyici işlevlerini sanal ağınızda çalışan hizmetlere bağlamanıza olanak tanır.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Azure Işlevleri için çalışma zamanı tabanlı ölçeklendirmeyi nasıl etkinleştireceğinizi gösteren ekran görüntüsü.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Service Bus tetikleyicisi ve HTTP tetikleyicisi dağıtma

1. GitHub 'da aşağıdaki örnek depoya gidin. Bir işlev uygulaması ve iki işlev, bir HTTP tetikleyicisi ve bir Service Bus kuyruğu tetikleyicisi içerir.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. Sayfanın üst kısmında, GitHub hesabınızda veya kuruluşunuzda bu deponun bir çatalını oluşturmak için **çatalla** ' yı seçin.

1. İşlev uygulamanızda, sol taraftaki menüden **Dağıtım Merkezi**' ni seçin. Ardından **Ayarlar**' ı seçin.

1. **Ayarlar** sekmesinde, aşağıdaki tabloda gösterilen dağıtım ayarlarını kullanın.

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Kaynak** | GitHub | 2. adımdaki örnek kod için bir GitHub deposu oluşturmuş olmanız gerekir. | 
    | **Kuruluş**  | Myorganleştirme | Deponuzu teslim edilen kuruluş. Bu genellikle hesabıdır. |
    | **Depo** | myRepo | Örnek kod için oluşturduğunuz depo. |
    | **Dal** | main | Oluşturduğunuz deponun ana dalı. |
    | **Çalışma zamanı yığını** | .NET | Örnek kod C# ' de bulunur. |

1. **Kaydet**’i seçin. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Portal aracılığıyla Azure Işlevleri kodunun nasıl dağıtılacağı ekran görüntüsü.":::

1. İlk dağıtımınız birkaç dakika sürebilir. Uygulamanız başarıyla dağıtıldığında, **Günlükler** sekmesinde **başarılı (etkin)** bir durum iletisi görürsünüz. Gerekirse, sayfayı yenileyin.

Tebrikler! Örnek işlev uygulamanızı başarıyla dağıttınız.

## <a name="lock-down-your-function-app"></a>İşlev uygulamanızı kilitleme

Şimdi işlev uygulamanızı kilitlemek için özel uç noktası oluşturun. Bu özel uç nokta, özel bir IP adresi kullanarak işlev uygulamanızı sanal ağınıza özel olarak ve güvenli bir şekilde bağlayacaktır. 

Daha fazla bilgi için bkz. [Özel uç nokta belgeleri](../private-link/private-endpoint-overview.md).

1. İşlev uygulamanızda, sol taraftaki menüden **ağ**' ı seçin.

1. **Özel uç nokta bağlantıları** altında **Özel uç nokta bağlantılarınızı yapılandırın**' ı seçin.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="İşlev uygulaması özel uç noktasına nasıl gidebileceğiniz ekran görüntüsü.":::

1. **Add (Ekle)** seçeneğini belirleyin.

1. Açılan bölmede aşağıdaki özel uç nokta ayarlarını kullanın:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="İşlev uygulaması özel uç noktasının nasıl oluşturulacağı ekran görüntüsü. Ad functionapp-Endpoint ' dir. Abonelik ' Private test Sub CACHHAI '. Sanal ağ, MyVirtualNet öğreticisi. Alt ağ varsayılandır.":::

1. Özel uç noktayı eklemek için **Tamam ' ı** seçin. 
 
Tebrikler! İşlev uygulamanızı, Service Bus 'ı ve depolama hesabınızı özel uç noktalar ekleyerek başarıyla güvenli hale getirdi!

### <a name="test-your-locked-down-function-app"></a>Kilitli işlev uygulamanızı test etme

1. İşlev uygulamanızda, sol taraftaki menüden **işlevler**' i seçin.

1. **Servicebusqueuetrigger** öğesini seçin.

1. Soldaki menüde, **İzle**' yi seçin. 
 
Uygulamanızı izleyeceğinizi görürsünüz. Tarayıcınızın sanal ağa erişimi yok, bu nedenle sanal ağ içindeki kaynaklara doğrudan erişemez. 
 
İşte Application Insights kullanarak işlevinizi izlemenin alternatif bir yolu.

1. İşlev uygulamanızda, sol taraftaki menüden **Application Insights**' yi seçin. **Application Insights verileri görüntüle**' yi seçin.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Bir işlev uygulaması için Application Insights 'ın nasıl görüntüleneceği ekran görüntüsü.":::

1. Soldaki menüden **canlı ölçümler**' i seçin.

1. Yeni bir sekme açın. Service Bus 'da, sol taraftaki menüden **Kuyruklar**' ı seçin.

1. Kuyruğunuzu seçin.

1. Soldaki menüde **Service Bus gezgin**' i seçin. **Içerik türü** için **Gönder**' ın altında **metin/düz**' yı seçin. Sonra bir ileti girin. 

1. İletiyi göndermek için **Gönder** ' i seçin.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Portalı kullanarak Service Bus iletilerinin nasıl gönderileceğini gösteren ekran görüntüsü.":::

1. **Canlı ölçümler** sekmesinde Service Bus kuyruğu tetikleyicinizin tetiklentiğini görmeniz gerekir. Bu yoksa iletiyi **Service Bus Explorer**'dan yeniden gönderin.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="İşlev uygulamaları için canlı ölçümleri kullanarak iletilerin nasıl görüntüleneceği ekran görüntüsü.":::

Tebrikler! İşlev uygulaması kurulumunuzu özel uç noktalarla başarıyla test tamamladınız.

## <a name="understand-private-dns-zones"></a>Özel DNS bölgelerini anlama
Azure kaynaklarına bağlanmak için özel bir uç nokta kullandınız. Genel uç nokta yerine özel bir IP adresine bağlanıyorsunuz. Mevcut Azure Hizmetleri, genel uç noktaya bağlanmak için mevcut bir DNS kullanmak üzere yapılandırılmıştır. Özel uç noktaya bağlanmak için DNS yapılandırmasını geçersiz kılmanız gerekir.

Özel bir uç noktayla yapılandırılmış her bir Azure kaynağı için özel bir DNS bölgesi oluşturulur. Özel uç nokta ile ilişkili her özel IP adresi için bir DNS kaydı oluşturulur.

Bu öğreticide aşağıdaki DNS bölgeleri oluşturulmuştur:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir Premium işlev uygulaması, depolama hesabı ve hizmet veri yolu oluşturdunuz. Tüm bu kaynakları özel uç noktaların arkasında güvenli hale getirdi. 

Kullanılabilir ağ özellikleri hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kullanın:

> [!div class="nextstepaction"]
> [Azure Işlevlerinde ağ seçenekleri](./functions-networking-options.md)


> [!div class="nextstepaction"]
> [Azure Işlevleri Premium planı](./functions-premium-plan.md)
