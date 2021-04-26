---
author: baanders
description: Visual Studio 'dan bir Azure işlevi yayımlama işlemi için dosya ekleme
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: ddc56ab05a087c9e86d67a13aebcfb8e65fbd78f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480745"
---
Projeyi Azure 'da bir işlev uygulamasında yayımlamak için Çözüm Gezgini başlatın. Projeye sağ tıklayın ve ardından **Yayımla**' yı seçin.

> [!IMPORTANT] 
> Azure 'da bir işlev uygulamasına yayımlama, Azure dijital TWINS 'ten bağımsız olarak aboneliğiniz üzerinde ek ücretler doğurur.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Sağ tıklama çözüm menüsünü gösteren Visual Studio ekran görüntüsü. Menüsünde, Yayımla vurgulanacaktır.":::

Açılan **Yayımla** sayfasında, **Azure**'un varsayılan hedef seçimini bırakın. Sonra **İleri**’yi seçin. 

Belirli bir hedef için **Azure işlev uygulaması (Windows)** öğesini seçin ve ardından **İleri**' yi seçin.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Visual Studio 'nun Azure işlevi Yayımla iletişim kutusunu gösteren ekran görüntüsü. Belirli hedef sayfasında, seçim Azure İşlev Uygulaması (Windows) ' dir.":::

**İşlevler örneği** sekmesinde, aboneliğinizi seçin. Ardından yeni bir işlev oluşturmak için artı (+) simgesini seçin.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Visual Studio 'nun Azure işlevi Yayımla iletişim kutusunu gösteren ekran görüntüsü. Artı simgesi vurgulanır.":::

**İşlev uygulaması (Windows)-yeni oluştur** penceresinde aşağıdaki alanları girin:
* **Ad** , Azure 'un Azure işlevleri uygulamanızı barındırmak için kullanacağı tüketim planının adıdır. Bu ad, gerçek işlevinizi tutan işlev uygulaması için de geçerli olacaktır. Benzersiz bir değer seçebilir veya varsayılan öneriyi bırakabilirsiniz.
* **Aboneliğin** , kullanmak istediğiniz abonelikle eşleştiğinden emin olun. 
* **Kaynak grubunun** kullanmak Istediğiniz bir kaynak olduğundan emin olun.
* **Plan türü** seçimini **Tüketim** olarak bırakın.
* Kaynak grubunuzun **konumunu** seçin.
* **Yeni** bağlantıyı seçerek yeni bir **Azure depolama** kaynağı oluşturun. Konumu kaynak grubunuza uyacak şekilde ayarlayın, diğer varsayılan değerleri kullanın ve ardından **Tamam**' ı seçin.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Visual Studio 'nun Azure işlevi Yayımla iletişim kutusunu gösteren ekran görüntüsü. Yeni bir işlev uygulamasının ayrıntıları, ad, abonelik, kaynak grubu, plan türü, konum ve Azure depolama dahil olmak üzere doldurulmuştur.":::

Ardından **Oluştur**’u seçin.

App Service oluşturulduktan sonra, **işlevler örneği** sekmesi açılır. Yeni işlev uygulamanız, kaynak grubunuzun altındaki **uygulamalar** alanında görüntülenir. **Son**'u seçin.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Visual Studio 'nun Azure işlevi Yayımla iletişim kutusunu gösteren ekran görüntüsü. Işlevler örneği sekmesi seçilidir. Yeni işlevler uygulaması, kaynak grubunun altında görüntülenir.":::

Ana Visual Studio penceresinde açılan **Yayımla** bölmesinde tüm bilgilerin doğru göründüğünden emin olun. Ardından **Yayımla**’yı seçin.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Visual Studio 'nun Yayımla bölmesini gösteren ekran görüntüsü. Yayımla düğmesi vurgulanır.":::

> [!NOTE]
> Aşağıdaki örnekte olduğu gibi bir açılır pencere görürseniz, **kimlik bilgilerini Azure 'dan almayı dene** ' yi seçin ve ardından **Kaydet**' i seçin.
> :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Visual Studio 'nun yayımlama kimlik bilgileri adlı bir açılan pencereyi gösteren ekran görüntüsü. Bir Kullanıcı adı ve parola için alanlar içerir. Ayrıca, Azure 'dan kimlik bilgilerini almayı denemek için bir düğme içerir." border="false":::
>
> Aşağıdaki uyarılardan birini görürseniz, en son Azure Işlevleri çalışma zamanı sürümüne yükseltmek için istemleri izleyin:
> * "Azure 'da Işlevleri Yükselt sürümü."
> * "İşlevleri çalışma zamanının sürümünüz Azure 'da çalışan sürümle eşleşmiyor."
>
> Bu uyarılar, Visual Studio 'nun eski bir sürümünü kullanıyorsanız görünebilir.

İşlev uygulamanız artık Azure 'da yayımlandı.
