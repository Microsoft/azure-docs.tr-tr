---
title: Anahtar Kasası kullanarak blob depolamaya erişme-Azure Databricks
description: Bu öğreticide, anahtar kasasında depolanan gizli dizileri kullanarak Azure Databricks Azure Blob depolamaya nasıl erişebileceğiniz açıklanmaktadır.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 7983e18066578e3f036da84c73b6554ead2c40a1
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791565"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Öğretici: Azure Key Vault kullanarak Azure Databricks Azure Blob depolamaya erişme

Bu öğreticide, anahtar kasasında depolanan gizli dizileri kullanarak Azure Databricks Azure Blob depolamaya nasıl erişebileceğiniz açıklanmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Depolama hesabı ve BLOB kapsayıcısı oluşturma
> * Azure Key Vault oluşturma ve gizli anahtar ekleme
> * Azure Databricks çalışma alanı oluşturma ve gizli dizi kapsamı ekleme
> * Azure Databricks blob kapsayıcınıza erişin

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure Portal](https://portal.azure.com/)’ında oturum açın.

> [!Note]
> Bu öğretici **Azure Ücretsiz deneme aboneliği**kullanılarak gerçekleştirilemez.
> Ücretsiz hesabınız varsa, profilinize gidin ve aboneliğinizi **Kullandıkça Öde**ile değiştirin. Daha fazla bilgi için bkz. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/). Ardından, [harcama limitini kaldırın](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)ve bölgenizdeki vCPU 'lar için [bir kota artışı isteyin](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) . Azure Databricks çalışma alanınızı oluşturduğunuzda, çalışma alanına 14 gün boyunca ücretsiz Premium Azure Databricks DBUs erişimi sağlamak için **deneme (Premium-14 gün ücretsiz DBUs)** fiyatlandırma katmanını seçebilirsiniz.

## <a name="create-a-storage-account-and-blob-container"></a>Depolama hesabı ve BLOB kapsayıcısı oluşturma

1. Azure portal **kaynak oluştur** > **depolama**' yı seçin. Ardından **depolama hesabı**' nı seçin.

   ![Azure depolama hesabı kaynağını bulma](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Aboneliğinizi ve kaynak grubunuzu seçin veya yeni bir kaynak grubu oluşturun. Ardından bir depolama hesabı adı girin ve bir konum seçin. **Gözden geçir + oluştur**' u seçin.

   ![Depolama hesabı özelliklerini ayarla](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Doğrulama başarısız olursa, sorunları çözün ve yeniden deneyin. Doğrulama başarılı olursa **Oluştur** ' u seçin ve depolama hesabının oluşturulmasını bekleyin.

4. Yeni oluşturduğunuz depolama hesabınıza gidin ve **genel bakış** sayfasındaki **Hizmetler** altında **Bloblar** ' ı seçin. Ardından **+ kapsayıcı** ' yı seçin ve bir kapsayıcı adı girin. **Tamam**’ı seçin.

   ![Yeni kapsayıcı oluştur](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. BLOB depolama kapsayıcınıza yüklemek istediğiniz dosyayı bulun. Bir dosyanız yoksa, bazı bilgilerle yeni bir metin dosyası oluşturmak için bir metin düzenleyicisi kullanın. Bu örnekte, **HW. txt** adlı bir dosya "Hello World" metnini içerir. Metin dosyanızı yerel olarak kaydedin ve BLOB depolama kapsayıcınıza yükleyin.

   ![Dosyayı kapsayıcıya yükle](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Depolama hesabınıza dönün ve **Ayarlar**altında **erişim anahtarları** ' nı seçin. **Depolama hesabı adı** ve **anahtar 1** ' i daha sonra bu öğreticide kullanmak üzere bir metin düzenleyicisine kopyalayın.

   ![Depolama hesabı erişim anahtarlarını bul](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Azure Key Vault oluşturma ve gizli anahtar ekleme

1. Azure portal, **kaynak oluştur** ' u seçin ve arama kutusuna **Key Vault** girin.

   ![Azure Kaynak arama kutusu oluşturma](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. Key Vault kaynağı otomatik olarak seçilir. **Oluştur**'u seçin.

   ![Key Vault kaynağı oluşturma](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. **Anahtar Kasası oluştur** sayfasında, aşağıdaki bilgileri girin ve kalan alanlar için varsayılan değerleri tutun:

   |Özellik|Açıklama|
   |--------|-----------|
   |Adı|Anahtar kasanızın benzersiz adı.|
   |Abonelik|Bir abonelik seçin.|
   |Kaynak grubu|Bir kaynak grubu seçin veya yeni bir tane oluşturun.|
   |Konum|Konum seçin.|

   ![Azure Anahtar Kasası özellikleri](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Yukarıdaki bilgileri girdikten sonra **Oluştur**’u seçin. 

4. Azure portal yeni oluşturduğunuz anahtar kasanıza gidin ve **gizlilikler**' ı seçin. Ardından **+ Oluştur/Içeri aktar**' ı seçin. 

   ![Yeni Anahtar Kasası gizli dizisi oluştur](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Gizli dizi **Oluştur** sayfasında aşağıdaki bilgileri sağlayın ve kalan alanlar için varsayılan değerleri tutun:

   |Özellik|Değer|
   |--------|-----------|
   |Karşıya yükleme seçenekleri|Manual|
   |Adı|Depolama hesabı anahtarınız için kolay ad.|
   |Değer|depolama hesabınızdan key1.|

   ![Yeni Anahtar Kasası gizli dizisi özellikleri](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Anahtar adını bu öğreticide daha sonra kullanmak üzere bir metin düzenleyicisinde kaydedin ve **Oluştur**' u seçin. Ardından, **Özellikler** menüsüne gidin. **DNS adını** ve **kaynak kimliğini** öğreticide daha sonra kullanmak üzere bir metin düzenleyicisine kopyalayın.

   ![Azure Key Vault DNS adını ve kaynak KIMLIĞINI Kopyala](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Azure Databricks çalışma alanı oluşturma ve gizli dizi kapsamı ekleme

1. Azure portalında **Kaynak oluşturun** > **Analiz** > **Azure Databricks**'i seçin.

    ![Azure portal databricks](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. **Azure Databricks hizmeti**altında, Databricks çalışma alanı oluşturmak için aşağıdaki değerleri sağlayın.

   |Özellik  |Açıklama  |
   |---------|---------|
   |Çalışma alanı adı     | Databricks çalışma alanınız için bir ad sağlayın        |
   |Abonelik     | Açılan listeden Azure aboneliğinizi seçin.        |
   |Kaynak grubu     | Anahtar kasanızı içeren kaynak grubunu seçin. |
   |Konum     | Azure Key Vault aynı konumu seçin. Tüm kullanılabilir bölgeler için bkz. [bölgeye göre kullanılabilir Azure hizmetleri](https://azure.microsoft.com/regions/services/).        |
   |Fiyatlandırma Katmanı     |  **Standart** veya **Premium** arasında seçim yapın. Bu katmanlar hakkında daha fazla bilgi için bkz. [Databricks fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Databricks çalışma alanı özellikleri](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   **Oluştur**'u seçin.

3. Azure portal yeni oluşturulan Azure Databricks kaynağına gidin ve **çalışma alanını Başlat**' ı seçin.

   ![Azure Databricks çalışma alanını Başlat](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Azure Databricks çalışma alanınız ayrı bir pencerede açıldıktan sonra URL 'ye **#secrets/createScope** ekleyin. URL aşağıdaki biçimde olmalıdır: 

   **https://< \Location >. azuredatabricks. net/? o = < \ID > #secrets/createScope**.

5. Bir kapsam adı girin ve daha önce kaydettiğiniz Azure Key Vault DNS adını ve kaynak KIMLIĞINI girin. Kapsam adını bu öğreticide daha sonra kullanmak üzere bir metin düzenleyicisinde kaydedin. Ardından **Oluştur**’u seçin.

   ![Azure Databricks çalışma alanında gizli kapsam oluştur](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Azure Databricks blob kapsayıcınıza erişin

1. Azure Databricks çalışma alanınızın ana sayfasında, **ortak görevler**altında **Yeni küme** ' ı seçin.

   ![Yeni bir Azure Databricks Not defteri oluşturun](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Bir küme adı girin ve **küme oluştur**' u seçin. Küme oluşturma işleminin tamamlanabilmesi birkaç dakika sürer.

3. Küme oluşturulduktan sonra, Azure Databricks çalışma alanınızın giriş sayfasına gidin, **ortak görevler**altında **Yeni Not defteri** ' ni seçin.

   ![Yeni bir Azure Databricks Not defteri oluşturun](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Bir not defteri adı girin ve dili Python olarak ayarlayın. Kümeyi, önceki adımda oluşturduğunuz kümenin adına ayarlayın.

5. BLOB depolama kapsayıcınızı bağlamak için aşağıdaki komutu çalıştırın. Aşağıdaki özellikler için değerleri değiştirmeyi unutmayın:

   * kapsayıcının adı
   * depolama hesabınızın adı
   * takma ad
   * yapılandırma anahtarı
   * kapsam adı
   * anahtar adı

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **takma ad** , BLOB depolama kapsayıcısının veya kapsayıcı içindeki bir klasörün (kaynakta belirtilen) nereye bağlandığını temsil eden BIR dBFS yoludur.
   * **conf-key** `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` ya da `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net` olabilir
   * **kapsam adı** , önceki bölümde oluşturduğunuz gizli kapsamın adıdır. 
   * **anahtar adı** , anahtar kasasındaki depolama hesabı anahtarı için oluşturduğunuz gizli anahtar adı.

   ![Not defterinde BLOB depolama alanı bağlama oluştur](./media/store-secrets-azure-key-vault/command1.png)

6. BLOB depolama kapsayıcıınızda bulunan metin dosyasını bir veri çerçevesine okumak için aşağıdaki komutu çalıştırın. Komutdaki değerleri, bağlama adı ve dosya adınızla eşleşecek şekilde değiştirin.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Dosyayı dataframe 'e oku](./media/store-secrets-azure-key-vault/command2.png)

7. Dosyanızın içeriğini göstermek için aşağıdaki komutu kullanın.

   ```python
   df.show()
   ```
   ![Veri çerçevesini göster](./media/store-secrets-azure-key-vault/command3.png)

8. BLOB depolama alanınızı çıkarmak için aşağıdaki komutu çalıştırın:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Depolama hesabının bağlantısını kaldır](./media/store-secrets-azure-key-vault/command4.png)

9. Bağlama çıkarıldıktan sonra BLOB depolama hesabınızdan artık okuyabildiğinize dikkat edin.

   ![Depolama hesabı çıkarma hatası](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, tüm kaynak grubunuzu aşağıdaki adımlarla silin:

1. Azure portal sol taraftaki menüden **kaynak grupları** ' nı seçin ve kaynak grubunuza gidin.

2. **Kaynak grubunu sil** ' i seçin ve kaynak grubu adınızı yazın. Ardından **Sil**’i seçin. 

## <a name="next-steps"></a>Sonraki adımlar

Cosmos DB için etkinleştirilmiş bir hizmet uç noktası ile VNet 'e eklenmiş Databricks ortamının nasıl uygulanacağını öğrenmek için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [Öğretici: Cosmos DB uç noktası ile Azure Databricks uygulama](service-endpoint-cosmosdb.md)
