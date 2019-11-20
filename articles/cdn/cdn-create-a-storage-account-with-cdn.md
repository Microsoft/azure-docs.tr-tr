---
title: Hızlı başlangıç-Azure CDN bir Azure Depolama hesabını tümleştirme
description: Azure İçerik Teslim Ağı'nı (CDN) Azure Depolama'dan önbelleğe blob alarak yüksek bant genişlikli içerik sunmak için kullanmayı öğrenin.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/24/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 729ae34d90fd2415fc44fc75391950f18fee6fc0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083069"
---
# <a name="quickstart-integrate-an-azure-storage-account-with-azure-cdn"></a>Hızlı Başlangıç: Azure depolama hesabını Azure CDN ile tümleştirme
Bu hızlı başlangıçta [Azure İçerik Teslim Ağı'nı (CDN)](cdn-overview.md) Azure depolamadan önbelleğe içerik almak için etkinleştireceksiniz. Azure CDN, geliştiricilere yüksek bant genişlikli içerik sunmak için genel bir çözüm sunmaktadır. İşlem örneklerinin bloblarını ve statik içeriğini Birleşik Devletler, Avrupa, Asya, Avustralya ve Güney Amerika'daki fiziksel düğümlerde önbelleğe alabilir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Azure portalında oturum açma
Azure hesabınızla [Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma
Bir Azure aboneliğine yönelik yeni bir depolama hesabı oluşturmak için aşağıdaki yordamı kullanın. Bir depolama hesabı Azure Depolama hizmetlerine erişmenizi sağlar. Depolama hesabı Azure Blob, Kuyruk ve Tablo depolama Azure Depolama hizmeti bileşenlerinin her birine erişmek için en yüksek düzeydeki ad alanını temsil eder. Daha fazla bilgi için bkz: [Microsoft Azure Storage'a giriş](../storage/common/storage-introduction.md).

Bir depolama hesabı oluşturmak için hizmetin yöneticisi veya ilişkili aboneliğin bir ortak yöneticisi olmanız gerekir.

Bir depolama hesabı oluşturmak için Azure portal ve PowerShell dahil olmak üzere çeşitli yöntemler kullanabilirsiniz. Bu hızlı başlangıç, Azure portalın nasıl kullanılacağını göstermektedir.   

**Bir Azure aboneliğine bir depolama hesabı oluşturmak için**

1. Azure portalında sol üst köşedeki **Kaynak oluştur** seçeneğini belirleyin. 

    **Yeni** bölmesi görüntülenir.

2. **Depolama**’yı ve sonra **Depolama hesabı - blob, dosya, tablo, kuyruk** öğesini seçin.
    
    ![Depolama kaynağını seçin](./media/cdn-create-a-storage-account-with-cdn/cdn-select-new-storage-account.png)

    **Depolama hesabı oluşturma** bölmesi görünür.   

    ![Depolama hesabı oluşturma bölmesi](./media/cdn-create-a-storage-account-with-cdn/cdn-create-new-storage-account.png)

3. **Ad** kutusuna bir alt etki alanı adı girin. Bu giriş, 3-24 küçük harf ve rakam içerebilir.
   
    Bu değer, aboneliğin blob, kuyruk veya tablo kaynağı adreslerine ulaşmak için kullanılan URI içindeki konak adı olur. Blob depolamadaki bir kapsayıcı kaynağının adresine ulaşmak için aşağıdaki biçimde bir URI kullanın:
   
    http:// *&lt;StorageAcountLabel&gt;* .blob.core.windows.net/ *&lt;kapsayicim&gt;*

    Burada  *&lt;StorageAccountLabel&gt;* , **Ad** kutusuna girdiğiniz değeri gösterir.
   
    > [!IMPORTANT]    
    > URL etiketi, depolama hesabının URI'sinin alt etki alanını oluşturur ve Azure'da barındırılan tüm hizmetler arasında benzersiz olması gerekir.
   
    Bu değer ayrıca portalda depolama hesabının adı olarak veya programlamada bu hesaba erişirken de kullanılır.
    
4. Ayarların geri kalanı için aşağıdaki tabloda belirtilen değerleri kullanın:

    | Ayar  | Değer |
    | -------- | ----- |
    | **Dağıtım modeli** | Varsayılan değeri kullanın. |
    | **Hesap türü** | Varsayılan değeri kullanın. |
    | **Konum**    | Açılan listeden **Orta ABD**’yi seçin. |
    | **Çoğaltma** | Varsayılan değeri kullanın. |
    | **Performans** | Varsayılan değeri kullanın. |
    | **Güvenli aktarım gereklidir** | Varsayılan değeri kullanın. |
    | **Abonelik** | Açılan listeden bir Azure aboneliği seçin. |
    | **Kaynak grubu** | **Yeni oluştur**’u seçin ve kaynak grubu adınız olarak *my-resource-group-123* girin. Bu adın küresel olarak benzersiz olması gerekir. Ad zaten kullanılıyorsa, farklı bir ad girebilir ya da **Var olanı kullan**'ı seçip açılan listeden **my-resource-group-123**'ü seçebilirsiniz. <br />Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure Resource Manager’a genel bakış](../azure-resource-manager/resource-group-overview.md#resource-groups).| 
    | **Sanal ağları yapılandırma** | Varsayılan değeri kullanın. |  
    
5. Oluşturulduktan sonra depolama hesabını panonuza kaydetmek için **Panoya sabitle**'yi seçin.
    
6. **Oluştur**’u seçin. Depolama hesabını oluşturmayı tamamlamak birkaç dakika alabilir.

## <a name="enable-azure-cdn-for-the-storage-account"></a>Depolama hesabı için Azure CDN'yi etkinleştirme

Azure CDN'yi doğrudan depolama hesabınızdan depolama hesabınız için etkinleştirebilirsiniz. CDN uç noktanız için [büyük dosya indirmeyi en iyi duruma getirme](cdn-optimization-overview.md#large-file-download) gibi gelişmiş yapılandırma ayarları belirtmek istiyorsanız, bir CDN profili ve uç noktası oluşturmak üzere bunun yerine [Azure CDN uzantısını](cdn-create-new-endpoint.md) kullanabilirsiniz.

1. Panodan bir depolama hesabını ve ardından sol bölmeden **Azure CDN**'yi seçin. **Azure CDN** düğmesi hemen görünmüyorsa, bulmak için CDN'yi sol bölmenin **Arama** kutusuna girebilirsiniz.
    
    **Azure CDN** sayfası görünür.

    ![CDN uç noktası oluşturma](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)
    
2. Aşağıdaki tabloda belirtilen gerekli bilgileri girerek yeni bir uç noktası oluşturun:

    | Ayar  | Değer |
    | -------- | ----- |
    | **CDN profili** | **Yeni oluştur** ' u seçin ve profil adınızı girin, örneğin, *My-CDN-profile-123*. Bu adın küresel olarak benzersiz olması gerekir.  |
    | **Fiyatlandırma katmanı** | Açılan listeden **Standart Verizon**’u seçin. |
    | **CDN uç noktası adı** | Uç nokta ana bilgisayar adını girin (örneğin, *My-Endpoint-123*). Bu adın küresel olarak benzersiz olması gerekir. Bu ad, _&lt;uç nokta adı&gt;_ .azureedge.net etki alanındaki önbelleğe alınmış kaynaklarınıza erişmek için kullanılır. |
    | **Kaynak konak adı** | Yeni CDN uç noktası, kaynak sunucu için varsayılan olarak depolama hesabınızın konak adını kullanır. |

3. **Oluştur**’u seçin. Uç nokta oluşturulduktan sonra uç nokta listesinde görünür.

    ![Depolama yeni CDN uç noktası](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)


## <a name="enable-additional-cdn-features"></a>Ek CDN özelliklerini etkinleştirme
CDN uç noktası yapılandırma sayfasını açmak için depolama hesabı **Azure CDN** sayfasında listeden CDN uç noktasını seçin. Bu sayfadan [sıkıştırma](cdn-improve-performance.md), [sorgu dizesi önbelleğe alma](cdn-query-string.md) ve [coğrafi filtreleme](cdn-restrict-access-by-country.md) gibi ek CDN özelliklerini sunduğunuz içerik için etkinleştirebilirsiniz. 
    
![Depolama CDN uç noktası yapılandırması](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)

## <a name="enable-sas"></a>SAS'yi etkinleştirme
Özel depolama kapsayıcılarına sınırlı erişim vermek istiyorsanız, Azure depolama hesabınızın Paylaşılan Erişim İmzası (SAS) özelliğini kullanabilirsiniz. Bir SAS, hesap anahtarınızı açığa çıkarmadan Azure Depolama kaynaklarınıza kısıtlı erişim hakları veren bir URI'dir. Daha fazla bilgi için bkz. [Azure CDN'yi SAS ile kullanma](cdn-sas-storage-support.md).

## <a name="access-cdn-content"></a>CDN içeriğine erişme
CDN'de önbelleğe alınmış içeriğe erişmek için portalda verilen CDN URL'sini kullanın. Önbelleğe alınan bir blobun adresi aşağıdaki biçimdedir:

http://<*Ucnoktaadi*\>.azureedge.net/<*genelKapsayicim*\>/<*BlobAdi*\>

> [!NOTE]
> Bir depolama hesabına Azure CDN erişimini etkinleştirdikten sonra herkesin erişebildiği nesneler CDN POP önbelleğine almaya uygundur. CDN'de önbelleğe alınmış durumdaki bir nesneyi değiştirirseniz, Azure CDN, yaşam süresinin dolmasından sonra önbelleğe alınan içeriği yenileyene kadar yeni içerik kullanılamaz.

## <a name="remove-content-from-azure-cdn"></a>Azure CDN'den içerik kaldırma
Bir nesneyi Azure CDN'de artık önbelleğe almak istemiyorsanız, aşağıdaki adımlardan birini uygulayabilirsiniz:

* Kapsayıcıyı genel yerine özel yapma. Daha fazla bilgi için bkz. [Kapsayıcılara ve bloblara anonim okuma erişimini yönetme](../storage/blobs/storage-manage-access-to-resources.md).
* Azure portalı kullanarak CDN uç noktasını devre dışı bırakın veya silin.
* Barındırılan hizmetinizi nesne için isteklere artık yanıt vermeyecek şekilde değiştirin.

Azure CDN'de daha önce önbelleğe alınmış bir nesne, yaşam süresi sona erene veya uç nokta [temizlenene](cdn-purge-endpoint.md) kadar önbellekte kalır. Yaşam süresi sona erdiğinde, Azure CDN, CDN uç noktasının hala geçerli ve nesnenin hala anonim olarak erişilebilir durumda olup olmadığını belirler. Bunlar söz konusu değilse nesne artık önbelleğe alınmaz.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Önceki adımlarda, bir kaynak grubunda CDN profili ve bir uç nokta oluşturdunuz. [Sonraki adımlara](#next-steps) gidip uç noktanıza nasıl özel etki alanı ekleneceğini öğrenmek istiyorsanız bu kaynakları kaydedin. Ancak ileride bu kaynakları kullanmayı düşünmüyorsanız, kaynak grubunu silerek bunları silebilir, böylece ek ücretleri önleyebilirsiniz:

1. Azure portalındaki sol menüden **Kaynak grupları**’nı seçin ve sonra **my-resource-group-123** seçeneğini belirleyin.

2. **Kaynak grubu** sayfasında **Kaynak grubunu sil**’i seçin, metin kutusuna *my-resource-group-123* değerini girin ve **Sil**’i seçin.

    Bu işlem, bu hızlı başlangıçta oluşturduğunuz kaynak grubunu, profili ve uç noktayı siler.

3. Depolama hesabınızı silmek için, hesabı panodan seçin, sonra üstteki menüden **Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar
CDN uç noktanıza özel etki alanı ekleme ve uç noktada HTTPS'yi etkinleştirme hakkında bilgi edinmek için aşağıdaki öğreticiye bakın:

> [!div class="nextstepaction"]
> [Öğretici: Depolama bloblarına HTTPS üzerinden Azure CDN özel etki alanı kullanarak erişme](cdn-storage-custom-domain-https.md)

