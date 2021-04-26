---
title: Microsoft Depolama Gezgini ile kapsayıcılar ve BLOB 'lar için paylaşılan erişim imzası (SAS) belirteci oluşturma
description: Microsoft Depolama Gezgini ve Azure portal ile kapsayıcılar ve Bloblar için paylaşılan erişim belirteci (SAS) oluşturma
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: e40fc569ad1c8ec5894f06915422bea37cfc40ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102489642"
---
# <a name="create-sas-tokens-for-document-translation-processing"></a>Belge çevirisi işleme için SAS belirteçleri oluştur

Bu makalede, Azure Depolama Gezgini veya Azure portal kullanarak paylaşılan erişim imzası (SAS) belirteçleri oluşturmayı öğreneceksiniz. Bir SAS belirteci, Azure Depolama hesabınızdaki kaynaklara güvenli, temsilcili erişim sağlar.

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Azure Depolama Gezgini ile SAS belirteçlerinizi oluşturma

### <a name="prerequisites"></a>Önkoşullar

* Windows, macOS veya Linux geliştirme ortamınızda yüklü bir [**Azure Depolama Gezgini**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) uygulamasına ihtiyacınız olacaktır. Azure Depolama Gezgini, Azure bulut depolama kaynaklarınızı kolayca yönetmenizi sağlayan ücretsiz bir araçtır.
* Azure Depolama Gezgini uygulaması yüklendikten sonra, belge çevirisi için kullandığınız [depolama hesabına bağlanın](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) .

### <a name="create-your-tokens"></a>Belirteçlerinizi oluşturma

### <a name="sas-tokens-for-containers"></a>[Kapsayıcılar için SAS belirteçleri](#tab/Containers)

1. Azure Depolama Gezgini uygulamasını yerel makinenizde açın ve bağlı **depolama hesaplarınıza** gidin.
1. Depolama hesapları düğümünü genişletin ve **BLOB kapsayıcıları**' nı seçin.
1. Blob kapsayıcıları düğümünü genişletin ve bir depolama **kapsayıcısı** düğümüne sağ tıklayın veya Seçenekler menüsünü görüntüleyin.
1. Seçenekler menüsünden **paylaşılan erişim Imzası al...** seçeneğini belirleyin.
1. **Paylaşılan erişim imzası** penceresinde aşağıdaki seçimleri yapın:
    * **Erişim ilkenizi** seçin (varsayılan değer None).
    * Oturum açan anahtarın **Başlangıç** ve **bitiş** tarihini ve saatini belirtin. Kısa bir süre önce, oluşturulduktan sonra SAS iptal edilmeden önce bir kısa süre için önerilir.
    * Başlangıç ve bitiş tarihi ve saati için **saat dilimini** seçin (varsayılan olarak yereldir).
    * Uygun onay kutusunu işaretleyerek ve/veya temizleyerek kapsayıcı **izinlerinizi** tanımlayın.
    * Gözden geçirin ve **Oluştur**' u seçin.

1. Kapsayıcıda **kapsayıcı** adı, **URI** ve **sorgu dizesiyle** yeni bir pencere görünür.  
1. **Kapsayıcıyı, URI 'yi ve sorgu dizesi değerlerini kopyalayıp güvenli bir konuma yapıştırın. Bunlar yalnızca bir kez görüntülenir ve pencere kapatıldıktan sonra alınamaz.**
1. SAS URL 'SI oluşturmak için, bir depolama hizmeti URL 'sine SAS belirtecini (URI) ekleyin.

### <a name="sas-tokens-for-blobs"></a>[Blob 'lar için SAS belirteçleri](#tab/blobs)

1. Azure Depolama Gezgini uygulamasını yerel makinenizde açın ve bağlı **depolama hesaplarınıza** gidin.
1. Depolama düğümünüz ' ı genişletin ve **BLOB kapsayıcıları**' nı seçin.
1. Blob kapsayıcıları düğümünü genişletin ve içeriği ana pencerede göstermek için bir **kapsayıcı** düğümü seçin.
1. SAS erişimini atamak istediğiniz blobu seçin ve Seçenekler menüsünü göstermek için sağ tıklayın.
1. Seçenekler menüsünden **paylaşılan erişim Imzası al...** seçeneğini belirleyin.
1. **Paylaşılan erişim imzası** penceresinde aşağıdaki seçimleri yapın:
    * **Erişim ilkenizi** seçin (varsayılan değer None).
    * Oturum açan anahtarın **Başlangıç** ve **bitiş** tarihini ve saatini belirtin. Kısa bir süre önce, oluşturulduktan sonra SAS iptal edilmeden önce bir kısa süre için önerilir.
    * Başlangıç ve bitiş tarihi ve saati için **saat dilimini** seçin (varsayılan olarak yereldir).
    * Uygun onay kutusunu işaretleyerek ve/veya temizleyerek kapsayıcı **izinlerinizi** tanımlayın.
    * Gözden geçirin ve **Oluştur**' u seçin.
1. Blob 'unuza ait **BLOB** adı, **URI** ve **sorgu dizesiyle** yeni bir pencere görüntülenir.  
1. **Blob, URI ve sorgu dizesi değerlerini kopyalayıp güvenli bir konuma yapıştırın. Bunlar yalnızca bir kez görüntülenir ve pencere kapatıldıktan sonra alınamaz.**
1. SAS URL 'SI oluşturmak için, bir depolama hizmeti URL 'sine SAS belirtecini (URI) ekleyin.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Azure portal blob 'lar için SAS belirteçleri oluşturun

> [!NOTE]
> Doğrudan Azure portal içindeki kapsayıcılar için SAS belirteçleri oluşturma şu anda desteklenmiyor. Ancak, [**Azure Depolama Gezgini**](#create-your-sas-tokens-with-azure-storage-explorer) bir SAS belirteci oluşturabilir veya görevi [programlı](../../../storage/blobs/sas-service-create.md)bir şekilde tamamlayabilirsiniz.

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

* Etkin bir [**Azure hesabı**](https://azure.microsoft.com/free/cognitive-services/).  Bir [**hesabınız yoksa ücretsiz bir hesap oluşturabilirsiniz**](https://azure.microsoft.com/free/).
* Bir [**Translator**](https://ms.portal.azure.com/#create/Microsoft) hizmet kaynağı (**bilişsel** hizmetler çoklu hizmet kaynağı değil.  *Bkz* . [yeni Azure kaynağı oluşturma](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* [**Azure Blob depolama hesabı**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). BLOB verilerinizi depolama hesabınızda depolamak ve düzenlemek için kapsayıcılar oluşturacaksınız.

### <a name="create-your-tokens"></a>Belirteçlerinizi oluşturma

[Azure Portal](https://ms.portal.azure.com/#home) gidin ve aşağıdaki gibi ilerleyin:  

 **Depolama hesabınızın** → **kapsayıcıları** **→** kapsayıcınızı **BLOB** →

1. Sayfanın üst kısmındaki menüden **SAS oluştur** ' u seçin.

1. **İmzalama yöntemi** → **Kullanıcı temsili anahtarı**' nı seçin.

1. Uygun onay kutusunu işaretleyerek ve/veya temizleyerek **izinleri** tanımlayın.

1. İmzalı anahtar **Başlangıç** ve **bitiş** zamanlarını belirtin.

1. **Izin VERILEN IP adresleri** alanı isteğe bağlıdır ve isteklerin kabul EDILECEĞI bir IP adresı veya IP adresi aralığı belirtir. İstek IP adresi SAS belirtecinde belirtilen IP adresi veya adres aralığıyla eşleşmiyorsa yetkilendirilmemiş olmaz.

1. **Izin verilen protokoller** alanı isteğe bağlıdır ve SAS ile yapılan bir istek için izin verilen protokolü belirtir. Varsayılan değer HTTPS 'dir.

1. Gözden geçirin **ve ardından SAS belirteci oluştur ve URL 'yi** seçin.

1. **BLOB SAS belirteci** sorgu dizesi ve **BLOB SAS URL 'si** pencerenin alt bölümünde görüntülenir.  

1. **Blob SAS belirtecini ve URL değerlerini kopyalayıp güvenli bir konuma yapıştırın. Bunlar yalnızca bir kez görüntülenir ve pencere kapatıldıktan sonra alınamaz.**

1. SAS URL 'SI oluşturmak için, bir depolama hizmeti URL 'sine SAS belirtecini (URI) ekleyin.

## <a name="learn-more"></a>Daha fazla bilgi edinin

* [Program aracılığıyla Bloblar veya kapsayıcılar için SAS belirteçleri oluşturma](../../../storage/blobs/sas-service-create.md)
* [Dizin, kapsayıcı veya blob izinleri](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Belge çevirisi ile çalışmaya başlama](get-started-with-document-translation.md)
>
>