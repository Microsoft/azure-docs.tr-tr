---
title: 'Hızlı başlangıç: bir uygulamada kullanılacak nesne bağlantıları modeli oluşturma'
description: Bu hızlı başlangıçta, bir 3B modelden nesne bağlantıları modeli oluşturmayı öğreneceksiniz.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 69d23b9d02eb176a2e42985ef5c3673e83d9bb7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607909"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>Hızlı başlangıç: 3B modelden nesne bağlantıları modeli oluşturma

Azure nesne bağlantıları, HoloLens için nesne açısından algılayan karma gerçeklik deneyimlerini etkinleştiren, 3B modelleri AI modellerine dönüştüren yönetilen bir bulut hizmetidir. Bu hızlı başlangıçta, C#/.NET Core SDK 'sını kullanarak bir 3B modelden bir nesne bağlantıları modelinin nasıl oluşturulacağı ele alınmaktadır.

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Nesne çıpası hesabı oluşturma
> * Bir 3B modeli nesne bağlantıları modeli oluşturmak için dönüştürme

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakileri yaptığınızdan emin olun:

* <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>Ile bir Windows makinesi.
* <a href="https://git-scm.com" target="_blank">Windows Için git</a>.
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">.NET Core 3,1 SDK</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-object-anchors-account"></a>Nesne çıpası hesabı oluşturma

İlk olarak, nesne bağlayıcıları hizmeti ile bir hesap oluşturmanız gerekir.

1. [Azure Portal](https://portal.azure.com/) gidin ve **kaynak oluştur**' u seçin.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="Yeni kaynak oluştur":::

2. **Nesne bağlantıları** kaynağını arayın.

   "Nesne bağlayıcıları" için arama yapın.

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="Nesne tutturucular kaynağını seçin":::

   Arama sonuçlarındaki **nesne bağlayıcıları** kaynağında, **Create-> nesne bağlayıcıları**' nı seçin.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Nesne bağlantıları kaynağı oluşturma":::

3. **Nesne çıpası hesabı** iletişim kutusunda:
    * Benzersiz bir kaynak adı girin.
    * Kaynağı iliştirmek istediğiniz aboneliği seçin.
    * Var olan bir kaynak grubunu oluşturun veya kullanın.
    * Kaynağınızın mevcut olmasını istediğiniz bölgeyi seçin.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Nesne çıpası kaynak hesabı ayrıntılarını girin":::

    Kaynağı oluşturmaya başlamak için **Oluştur** ' u seçin.

4. Kaynak oluşturulduktan sonra **Kaynağa git**'i seçin.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="Kaynağa git":::

5. Genel Bakış sayfasında:

   **Hesap etki alanını** bir yere göz atın. Buna daha sonra ihtiyacınız olacak.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Nesne Tutturucularınızın kaynağı için hesap etki alanını kopyalayın":::

   **Hesap kimliğini** bir yere göz atın. Buna daha sonra ihtiyacınız olacak.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="Nesne Tutturucularınızın kaynağı için hesap KIMLIĞINI kopyalayın":::

   **Erişim tuşları** sayfasına gidin ve **birincil anahtarı** bir yere göz atın. Buna daha sonra ihtiyacınız olacak.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="Nesne Tutturucularınızın kaynağı için hesap anahtarını kopyalayın":::

## <a name="get-the-sample-project"></a>Örnek projeyi al

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>3B modeli dönüştürme

Şimdi de devam edebilir ve 3B modelinizi dönüştürebilirsiniz.

1. `quickstarts/conversion/Conversion.sln`Visual Studio 'da açın. Bu çözüm bir C# konsol projesi içerir.

2. `Configuration.cs`Projenin kökünde bulunan dosyasını açın ve `set-me` aşağıdaki alanlardaki değerleri değiştirin:

   | Alan         | Açıklama                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | Yukarıda oluşturulan nesne çıpası hesabının **hesap etki alanı** . |
   | AccountId     | Yukarıda oluşturulan nesne çıpası hesabının **hesap kimliği** .     |
   | AccountKey    | Yukarıda oluşturulan nesne çıpası hesabının **birincil anahtarı**     |

   Doğrulanması gereken dört ek alan vardır:

    | Alan                    | Açıklama                       |
    | ---                      | ---                               |
    | Inputassetpath           | Yerel makinenizde bir 3B modelin mutlak yolu. Desteklenen dosya biçimleri,,, ve ' dir `fbx` `ply` `obj` `glb` `gltf` . |
    | AssetDimensionUnit       | 3B modelinizin ölçüm birimi. Tüm desteklenen ölçü birimlerine numaralandırma kullanılarak erişilebilir `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` . |
    | Yer çekimi                  | 3B modelin yerçekimi vektörünün yönü. Bu 3B vektör, modelinizin koordinat sisteminde aşağı yönde bir yön sağlar. Örneğin negatif, `y` modelin 3B alanındaki aşağı doğru yönü gösteriyorsa, bu değer olacaktır `Vector3(0.0f, -1.0f, 0.0f)` . |

3. 3D modelinizi karşıya yüklemek, hizmetle yeni bir dönüştürme işi kaydetmek ve tamamlanmasını beklemek için projeyi derleyin ve çalıştırın. İş tamamlandığında, nesne tutturucular modeli içinde belirtilen dosyanın yanına indirilir `InputAssetPath` . Aşağıdaki konsol çıktısına benzer bir şey görmeniz gerekir:

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Daha sonra başvurmak üzere **Iş kimliğini** bir yere unutmayın. Hata ayıklarken veya sorun giderirken faydalı olabilir.

4. İş başarıyla tamamlandıktan sonra, belirtilen çıkış konumunda biçimdeki bir dosya görmeniz gerekir `<Model-Filename-Without-Extension>_<JobID>.ou` . Örneğin, 3B modelinizin dosya adı ise `chair.ply` ve Iş kimliğiniz ise `00000000-0000-0000-0000-000000000000` hizmet çıkışları olur `chair_00000000-0000-0000-0000-000000000000.ou` .

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir nesne çıpası hesabı oluşturdunuz ve bir nesne çıpası modeli oluşturmak için bir 3B modeli dönüştürülüyordu. Bu modeli, karma gerçeklik uygulamanızdaki nesne bağlantıları SDK 'Sı ile tümleştirme hakkında bilgi edinmek için aşağıdaki makalelerden biriyle devam edin:

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [MRTK ile Unity HoloLens](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)
