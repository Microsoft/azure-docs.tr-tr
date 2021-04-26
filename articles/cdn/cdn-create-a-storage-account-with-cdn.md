---
title: 'Hızlı başlangıç: Azure Depolama hesabını Azure CDN ile tümleştirme'
description: Bu hızlı başlangıçta, Azure depolama 'daki Blobları önbelleğe alarak yüksek bant genişlikli içerik sunmak için Azure Content Delivery Network (CDN) kullanmayı öğrenin.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 444639d9e36b67f25ed9b82bd68102273c66ae00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951241"
---
# <a name="quickstart-integrate-an-azure-storage-account-with-azure-cdn"></a>Hızlı başlangıç: Azure Depolama hesabını Azure CDN ile tümleştirme

Bu hızlı başlangıçta, Azure depolama 'daki içerikleri önbelleğe almak için [azure Content Delivery Network (CDN)](cdn-overview.md) etkinleştirilir. Azure CDN, geliştiricilere yüksek bant genişlikli içerik sunmak için genel bir çözüm sunmaktadır. İşlem örneklerinin bloblarını ve statik içeriğini Birleşik Devletler, Avrupa, Asya, Avustralya ve Güney Amerika'daki fiziksel düğümlerde önbelleğe alabilir.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Azure hesabınızla [Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Bir depolama hesabı Azure Depolama hizmetlerine erişmenizi sağlar. Depolama hesabı Azure Blob, Kuyruk ve Tablo depolama Azure Depolama hizmeti bileşenlerinin her birine erişmek için en yüksek düzeydeki ad alanını temsil eder. Daha fazla bilgi için bkz: [Microsoft Azure Storage'a giriş](../storage/common/storage-introduction.md).

Bir depolama hesabı oluşturmak için hizmetin yöneticisi veya ilişkili aboneliğin bir ortak yöneticisi olmanız gerekir.

1. Azure portal, sol üst köşedeki **kaynak oluştur** ' u seçin. **Yeni** bölmesi görüntülenir.

1. **Depolama hesabı** araması yapın ve açılan listeden **depolama hesabı-blob, dosya, tablo, kuyruk ' ı** seçin. Sonra **Oluştur**' u seçin.
    
    ![Depolama kaynağını seçin](./media/cdn-create-a-storage-account-with-cdn/cdn-select-new-storage-account.png)

1. **Depolama hesabı oluştur bölmesinde**, aşağıdaki ayrıntıları girin:

    | Ayar | Değer | 
    | --- | --- |
    | Proje ayrıntıları > kaynak grubu | **Yeni oluştur** ' u seçin ve *cdnquickstart-RG* adını kullanın. İsterseniz var olan bir kaynak grubunu da kullanabilirsiniz. |
    | Örnek ayrıntıları > depolama hesabı adı | Hesap için yalnızca 3-24 küçük harf ve sayı kullanarak bir ad girin. Ad, Azure genelinde benzersiz olmalıdır ve abonelik için blob, kuyruk veya tablo kaynaklarını çözmek için kullanılan URL 'de ana bilgisayar adı olur. Blob depolamada bir kapsayıcı kaynağını ele almak için aşağıdaki biçimde bir URI kullanın: http://*&lt; storageAccountName &gt;*. blob.Core.Windows.net/*&lt; Container-Name &gt;*.
    | Örnek ayrıntıları > konumu | Aşağı açılan listeden yakınınızdaki bir Azure bölgesi seçin. |
    
    Tüm diğer ayrıntıları varsayılan değerlere bırakın ve ardından **gözden geçir + oluştur**' u seçin.

1. Depolama hesabı oluşturma işleminin tamamlanması birkaç dakika sürebilir. Oluşturma işlemi tamamlandıktan sonra, bir sonraki adım için depolama hesabının sayfasını açmak üzere **Kaynağa Git** ' i seçin.

## <a name="enable-azure-cdn-for-the-storage-account"></a>Depolama hesabı için Azure CDN'yi etkinleştirme

1. Depolama hesabınızın sayfasında, sol taraftaki menüden **BLOB hizmeti**  >  **Azure CDN** ' ni seçin. **Azure CDN** sayfası görünür.

    ![CDN uç noktası oluşturma](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)
    
1. **Yeni uç nokta** bölümünde aşağıdaki bilgileri girin:

    | Ayar  | Değer |
    | -------- | ----- |
    | **CDN profili** | **Yeni oluştur** ' u seçin ve profil adınızı (örneğin, *CDN-profile-123*) girin. Profil, uç noktaların koleksiyonudur. |
    | **Fiyatlandırma katmanı** | Standart **Microsoft** gibi **Standart** seçeneklerden birini seçin. |
    | **CDN uç noktası adı** | *CDN-Endpoint-123* gibi uç nokta ana bilgisayar adını girin. Bu ad, Azure 'da genel olarak benzersiz olmalıdır çünkü _&lt; bitiş noktası-adı &gt;_. azureedge.net URL 'sindeki önbelleğe alınmış kaynaklarınıza erişir. |
    | **Kaynak konak adı** | Yeni CDN uç noktası, kaynak sunucu için varsayılan olarak depolama hesabınızın konak adını kullanır. |

1. **Oluştur**’u seçin. Uç nokta oluşturulduktan sonra uç nokta listesinde görünür.

    ![Depolama yeni CDN uç noktası](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!TIP]
> CDN uç noktanız için [büyük dosya indirmeyi en iyi duruma getirme](cdn-optimization-overview.md#large-file-download) gibi gelişmiş yapılandırma ayarları belirtmek istiyorsanız, bir CDN profili ve uç noktası oluşturmak üzere bunun yerine [Azure CDN uzantısını](cdn-create-new-endpoint.md) kullanabilirsiniz.


## <a name="enable-additional-cdn-features"></a>Ek CDN özelliklerini etkinleştirme

CDN uç noktası yapılandırma sayfasını açmak için depolama hesabı **Azure CDN** sayfasında listeden CDN uç noktasını seçin.

Bu sayfadan [sıkıştırma](cdn-improve-performance.md), [sorgu dizesi önbelleğe alma](cdn-query-string.md) ve [coğrafi filtreleme](cdn-restrict-access-by-country.md) gibi ek CDN özelliklerini sunduğunuz içerik için etkinleştirebilirsiniz. 
    
## <a name="enable-sas"></a>SAS'yi etkinleştirme

Özel depolama kapsayıcılarına sınırlı erişim vermek istiyorsanız, Azure depolama hesabınızın paylaşılan erişim Imzası (SAS) özelliğini kullanabilirsiniz. Bir SAS, hesap anahtarınızı açığa çıkarmadan Azure Depolama kaynaklarınıza kısıtlı erişim hakları veren bir URI'dir. Daha fazla bilgi için bkz. [Azure CDN'yi SAS ile kullanma](cdn-sas-storage-support.md).

## <a name="access-cdn-content"></a>CDN içeriğine erişme

CDN'de önbelleğe alınmış içeriğe erişmek için portalda verilen CDN URL'sini kullanın. Önbelleğe alınan bir blobun adresi aşağıdaki biçimdedir:

http://<*uç noktası-adı* \> . azureedge.net/<*mypubliccontainer* \> /< *blobname*\>

> [!NOTE]
> Bir depolama hesabına Azure CDN erişimini etkinleştirdikten sonra herkesin erişebildiği nesneler CDN POP önbelleğine almaya uygundur. CDN'de önbelleğe alınmış durumdaki bir nesneyi değiştirirseniz, Azure CDN, yaşam süresinin dolmasından sonra önbelleğe alınan içeriği yenileyene kadar yeni içerik kullanılamaz.

## <a name="remove-content-from-azure-cdn"></a>Azure CDN'den içerik kaldırma

Bir nesneyi Azure CDN'de artık önbelleğe almak istemiyorsanız, aşağıdaki adımlardan birini uygulayabilirsiniz:

- Kapsayıcıyı genel yerine özel yapma. Daha fazla bilgi için bkz. [Kapsayıcılara ve bloblara anonim okuma erişimini yönetme](../storage/blobs/anonymous-read-access-configure.md).
- Azure portalı kullanarak CDN uç noktasını devre dışı bırakın veya silin.
- Barındırılan hizmetinizi nesne için isteklere artık yanıt vermeyecek şekilde değiştirin.

Azure CDN'de daha önce önbelleğe alınmış bir nesne, yaşam süresi sona erene veya uç nokta [temizlenene](cdn-purge-endpoint.md) kadar önbellekte kalır. Yaşam süresi sona erdiğinde, Azure CDN, CDN uç noktasının hala geçerli ve nesnenin hala anonim olarak erişilebilir durumda olup olmadığını belirler. Bunlar söz konusu değilse nesne artık önbelleğe alınmaz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Önceki adımlarda, bir kaynak grubunda CDN profili ve bir uç nokta oluşturdunuz. [Sonraki adımlara](#next-steps) gidip uç noktanıza nasıl özel etki alanı ekleneceğini öğrenmek istiyorsanız bu kaynakları kaydedin. Ancak ileride bu kaynakları kullanmayı düşünmüyorsanız, kaynak grubunu silerek bunları silebilir, böylece ek ücretleri önleyebilirsiniz:

1. Azure portal sol taraftaki menüden **kaynak grupları** ' nı seçin ve ardından * CDNQuickstart-RG * * öğesini seçin.

2. **Kaynak grubu** sayfasında, **kaynak grubunu sil**' i seçin, metin kutusuna *cdnquickstart-RG* girin ve **Sil**' i seçin.

    Bu işlem, bu hızlı başlangıçta oluşturduğunuz kaynak grubunu, profili ve uç noktayı siler.

3. Depolama hesabınızı silmek için, hesabı panodan seçin, sonra üstteki menüden **Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: bir Web uygulamasından statik içerik sağlamak için CDN kullanma](cdn-add-to-web-app.md)
