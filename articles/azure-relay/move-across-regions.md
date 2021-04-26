---
title: Bir Azure Relay ad alanını başka bir bölgeye taşıma
description: Bu makalede, bir Azure Relay ad alanını geçerli bölgeden başka bir bölgeye nasıl taşıyacağınız gösterilmektedir.
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89463928"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Bir Azure Relay ad alanını başka bir bölgeye taşıma
Bu makalede bir Azure Relay ad alanını bir bölgeden başka bir bölgeye nasıl taşıyacağınız gösterilmektedir. Üst düzey adımlar şunlardır:

1. Geçiş ad alanını bir Azure Resource Manager şablonuna **dışarı aktarın** .
1. Şablondaki kaynakların **konumunu (bölge) güncelleştirin** . Ayrıca, şablondaki tüm **dinamik** WCF geçişleri silin. 

    WCF geçişleri iki moda sahiptir. İlk modda, WCF geçişi Azure portal veya Azure Resource Manager şablonu kullanılarak açıkça oluşturulur. Azure portal **WCF geçişleri** sayfasında, bu moddaki bir geçiş Için **IsDynamic** özelliğinin **false** olarak ayarlandığını görürsünüz. 

    İkinci modda, belirli bir uç nokta adresini bir dinleyici (sunucu) bağladığında WCF geçişi otomatik olarak üretilir. Dinleyici geçişe bağlandığı sürece, geçişi Azure portal WCF geçişleri listesinde görürsünüz. Bu modda geçiş için, dinamik olarak oluşturulduğundan **IsDynamic** özelliği **true** olarak ayarlanır. Dinleyicinin bağlantısı kesildiğinde dinamik WCF geçişi dışarıda geçer. 
1. Şablonu kullanarak hedef bölgeye kaynakları **dağıtın** .

## <a name="prerequisites"></a>Önkoşullar
Hedef bölgede Azure Relay hizmetinin kullanılabildiğinden emin olun. [Bölgeye göre kullanılabilen ürünleri](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all)görüntüleyin. 
 
## <a name="prepare"></a>Hazırlama
Başlamak için Kaynak Yöneticisi şablonu dışarı aktarın. Bu şablon Azure Relay ad alanınızı tanımlayan ayarları içerir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Tüm kaynaklar** ' ı seçin ve sonra Azure Relay ad alanınızı seçin.
3. Sol menüdeki **Ayarlar** ' ın altında **şablonu dışarı aktar** ' ı seçin.
4. **Şablonu dışarı aktar** sayfasında **İndir** ' i seçin.

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Kaynak Yöneticisi şablonu indir":::
5. Portaldan indirdiğiniz. zip dosyasını bulun ve bu dosyayı seçtiğiniz bir klasöre ayıklayın. Bu ZIP dosyası Template ve Parameters JSON dosyalarını içerir. 
1. Ayıklanan klasörden dosya **template.js** seçtiğiniz bir düzenleyicide açın.
1. Özelliği için arama `location` yapın ve değeri bölgenin yeni adıyla değiştirin. Konum kodlarını almak için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/). Bir bölgenin kodu, boşluk içermeyen bölge adıdır, örneğin, `West US` eşittir `westus` .
1. **Dınamık WCF geçiş** kaynaklarının tanımlarını kaldırın (tür: `Microsoft.Relay/namespaces/WcfRelays` ). Dinamik WCF geçişleri, **geçişler** sayfasında **IsDynamic** özelliği **true** olarak ayarlanmış olan olanlardır. Aşağıdaki örnekte, **yankı hizmeti** dınamık bir WCF geçiştir ve tanımı şablondan kaldırılmalıdır. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="Dinamik geçişler":::

## <a name="move"></a>Taşı
Hedef bölgede bir geçiş ad alanı oluşturmak için şablonu dağıtın. 

1. Azure portal **kaynak oluştur**' u seçin.
2. **Market 'Te arama** yapmak için, arama metni için **şablon dağıtımı** yazın, **şablon dağıtımı (özel şablonları kullanarak dağıtın)** öğesini seçin ve ardından **ENTER** tuşuna basın.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Yeni şablon dağıtımı":::    
1. **Şablon dağıtımı** sayfasında **Oluştur**' u seçin.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Yeni şablon dağıtımı-oluştur düğmesi":::        
1. **Özel dağıtım** sayfasında, **düzenleyicide kendi şablonunuzu oluştur**' u seçin.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Düzenleyicide kendi şablonunuzu oluşturun-bağlantı":::            
1. **Şablonu Düzenle** sayfasında, araç çubuğunda **Dosya Yükle** ' yi seçin ve ardından son bölümde indirdiğiniz dosyanın **template.js** yüklemek için yönergeleri izleyin.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Şablon seç":::                
1. Şablonu kaydetmek için **Kaydet** ' i seçin. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Şablonu Kaydet":::                    
1. **Özel dağıtım** sayfasında, aşağıdaki adımları izleyin: 
    1. Bir Azure **aboneliği** seçin. 
    2. Var olan bir **kaynak grubunu** seçin veya bir kaynak grubu oluşturun. 
    3. Hedef **konumu** veya bölgeyi seçin. Var olan bir kaynak grubunu seçtiyseniz, bu ayar salt okunurdur. 
    4. **Ad alanı için** yeni bir ad girin.
    1. **Gözden geçir ve oluştur**’u seçin. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Kaynak Yöneticisi şablonu dağıtma":::
    1. **Gözden geçir + oluştur** sayfasında sayfanın alt kısmında **Oluştur** ' u seçin. 
    
## <a name="verify"></a>Doğrulama
1. Dağıtım başarılı olduktan sonra **kaynak grubuna git**' i seçin.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Kaynak grubu bağlantısına git":::    
1. **Kaynak grubu** sayfasında Azure Relay ad alanını seçin. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Azure Relay ad alanını seçin":::    
1. **Azure Relay ad alanı** sayfasında, karma BAĞLANTıLARıN ve WCF geçişlerinin oluşturulduğunu doğrulamak için sol menüdeki **karma bağlantılar** veya **WCF geçişleri** ' ni seçin. Şablonu içeri aktarmadan önce dinamik WCF geçişleri için tanımları silmeyi unuttuysanız, bunları **WCF geçişleri** sayfasında silin. İstemciler geçiş ad alanına bağlandıklarında dinamik WCF geçişleri otomatik olarak oluşturulur. 

## <a name="discard-or-clean-up"></a>Atma veya temizleme
Dağıtımdan sonra, baştan başlamak istiyorsanız, **hedef Azure Relay ad alanını** silebilir ve bu makalenin [hazırlama](#prepare) ve [taşıma](#move) bölümünde açıklanan adımları yineleyebilirsiniz.

Değişiklikleri uygulamak ve bir ad alanını taşımayı tamamlamak için kaynak bölgedeki **Azure Relay ad alanını** silin. 

Azure portal kullanarak bir Azure Relay ad alanını (kaynak veya hedef) silmek için:

1. Azure portal en üstündeki ara penceresinde **geçişler** yazın ve arama sonuçlarında **hizmetlerden** **geçişleri** seçin. Tüm Azure Relay ad alanlarını bir listede görürsünüz.
2. **Geçiş** sayfasını açmak için silinecek hedef ad alanını seçin. 
1. **Geçiş** sayfasında, araç çubuğundan **Sil** ' i seçin. 

    ![Ad alanını sil-düğme](./media/move-across-regions/delete-namespace-button.png)
3. **Ad alanını sil** sayfasında, silme işlemini onaylamak için Azure Relay ad alanının adını yazın ve ardından **Sil**' i seçin. 

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, bir Azure Relay ad alanını bir bölgeden başka bir bölgeye taşımış olursunuz. Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz:

- [Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md)
