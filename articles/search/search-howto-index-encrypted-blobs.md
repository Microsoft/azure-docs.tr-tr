---
title: Şifrelenmiş Azure Blob depolama içeriğini arama
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama ile Azure Blob depolama 'daki şifrelenmiş belgelerden metin dizinlemeyi ve ayıklamayı öğrenin.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: 4bab8def514df21d948d67f3cfba846c43917be2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96530944"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Azure Bilişsel Arama blob Dizinleyicileri ve becerileri kullanarak şifrelenmiş Blobların dizinini oluşturma

Bu makalede, Azure [bilişsel arama](search-what-is-azure-search.md) kullanarak daha önce [Azure Key Vault](../key-vault/general/overview.md)kullanılarak [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md) içinde şifrelenmiş olan belgeleri nasıl dizinleyen gösterilmektedir. Normalde, bir Dizin Oluşturucu şifreleme anahtarına erişimi olmadığından şifrelenmiş dosyalardan içerik ayıklayamaz. Bununla birlikte, [Decryptblobdosya](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) özel becerilerinden ve ardından [Belgetextractionbeceri](cognitive-search-skill-document-extraction.md)' ndan yararlanarak, dosyaların şifresini çözmek ve sonra içerikten ayıklanmak üzere anahtara denetimli erişim sağlayabilirsiniz. Bu, saklı belgelerinizin şifreleme durumuyla ödün vermeden bu belgeleri dizine alma imkanını kaldırır.

Azure Blob depolamada PDF, HTML, DOCX ve PPTX gibi daha önce şifrelenen tüm belgeler (yapılandırılmamış metin) ile başlayarak, bu kılavuzda aşağıdaki görevleri gerçekleştirmek için Postman ve arama REST API 'Leri kullanılmaktadır:

> [!div class="checklist"]
> * Belgelerin şifresini çözdüğü ve metinden çıkaran bir işlem hattı tanımlayın.
> * Çıktıyı depolamak için bir dizin tanımlayın.
> * Dizini oluşturmak ve yüklemek için işlem hattını yürütün.
> * Tam metin aramasını ve zengin sorgu söz dizimini kullanarak sonuçları keşfedebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) açın.

## <a name="prerequisites"></a>Önkoşullar

Bu örnekte, dosyalarınızı zaten Azure Blob depolama alanına yüklediğinizi ve bunları süreçte şifrelediğinizi varsaymış olursunuz. Dosyalarınızı başlangıçta karşıya yüklenmiş ve şifreli olarak almak için yardıma ihtiyacınız varsa, bunun nasıl yapılacağını öğrenmek için [Bu öğreticiye](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) göz atın.

+ [Azure Depolama](https://azure.microsoft.com/services/storage/)
+ Azure Bilişsel Arama ile aynı abonelikte [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) . Anahtar kasasında **geçici silme** ve **Temizleme koruması** etkinleştirilmiş olmalıdır.
+ [Faturalanabilir katmanda](search-sku-tier.md#tier-descriptions) [Azure bilişsel arama](search-create-service-portal.md) (herhangi bir bölgedeki temel veya üstü)
+ [Azure İşlevi](https://azure.microsoft.com/services/functions/)
+ [Postman masaüstü uygulaması](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1-hizmet oluşturma ve kimlik bilgilerini toplama

### <a name="set-up-the-custom-skill"></a>Özel yeteneği ayarlama

Bu örnek, [Azure Search Power yetenekler](https://github.com/Azure-Samples/azure-search-power-skills) GitHub deposundan örnek [Decryptblobdosya](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) projesini kullanır. Bu bölümde, bir beceri içinde kullanılabilmesi için yeteneği bir Azure Işlevine dağıtacaksınız. Yerleşik bir dağıtım betiği, **psdbf-Function-App** ile başlayan adlı bir Azure işlevi kaynağı oluşturur ve bu yeteneği yükler. Bir abonelik ve kaynak grubu sağlamanız istenecektir. Azure Key Vault örneğinizin içinde yaşadığı aboneliğin aynısını seçtiğinizden emin olun.

Bu şekilde, Decryptblobdosya becerisi her blob için URL ve SAS belirtecini giriş olarak alır ve indirilen, şifresi çözülmüş dosyayı Azure Bilişsel Arama beklediği dosya başvuru sözleşmesini kullanarak çıkarır. Şifre çözme işlemini gerçekleştirmek için Decryptblobdosya 'nın şifreleme anahtarına ihtiyacı olduğunu hatırlayın. Kurulum kapsamında, Azure Key Vault şifreleme anahtarına Decryptblobdosya işlevine erişimi veren bir erişim ilkesi de oluşturacaksınız.

1. Azure portal içinde sağlanmış Kaynak Yöneticisi şablonunu açacak olan [Decryptblobdosya giriş sayfasında](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment)bulunan **Azure 'a dağıt** düğmesine tıklayın.

1. **Azure Key Vault örneğinizin bulunduğu aboneliği** seçin (farklı bir abonelik seçerseniz bu kılavuz çalışmaz) ve var olan bir kaynak grubunu seçebilir ya da yeni bir tane oluşturursanız (yeni bir tane oluşturursanız, dağıtım için bir bölge seçmeniz gerekecektir).

1. **Gözden geçir + oluştur**' u seçin, koşulları kabul ettiğinizden emin olun ve ardından **Oluştur** ' u seçerek Azure işlevini dağıtın.

    ![Portalda ARM şablonu](media/indexing-encrypted-blob-files/arm-template.jpg "Portalda ARM şablonu")

1. Dağıtımın bitmesini bekleyin.

1. Portalda Azure Key Vault örneğinize gidin. Azure Key Vault, özel beceriye anahtar erişimi veren [bir erişim Ilkesi oluşturun](../key-vault/general/assign-access-policy-portal.md) .
 
    1. **Ayarlar** altında, **erişim ilkeleri**' ni seçin ve ardından **erişim ilkesi Ekle** ' yi seçin.
     
       ![Anahtar Kasası erişim ilkesi ekleme](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Anahtar Kasası erişim ilkeleri")

    1. **Şablondan Yapılandır** altında **Azure Data Lake Storage veya Azure Storage**' ı seçin.

    1. Sorumlu için, dağıttığınız Azure Işlev örneğini seçin. Bu dosyayı, **psdbf-Function-App** varsayılan ön ek değerine sahip 2. adımda oluşturmak için kullanılan kaynak önekini kullanarak arayabilirsiniz.

    1. Yetkili uygulama seçeneği için hiçbir şey seçmeyin.
     
        ![Anahtar Kasası erişim ilkesi şablonu ekleme](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Anahtar Kasası erişim ilkesi şablonu")

    1. Erişim ilkesini eklemek için erişim ilkeleri sayfasında **Kaydet** ' e tıkladığınızdan emin olun.
     
         ![Keykasası kaydetme erişimi ilkesi](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Anahtar Kasası erişim ilkesini kaydetme")

1. Portalda **psdbf-Function-App** işlevine gidin ve kılavuzda daha sonra ihtiyacınız olacak şekilde aşağıdaki özellikleri unutmayın:

    1. İşlevin ana sayfasında **temel parçalar** altında bulunan işlev URL 'si.
    
        ![İşlev URL'si](media/indexing-encrypted-blob-files/function-uri.jpg "Azure Işlevi URL 'sinin nerede bulunacağı")

    1. **Uygulama anahtarlarına** giderek, **varsayılan** anahtarı göstermek ve değeri kopyalamak için öğesine tıklanarak bulunan konak anahtar kodu.
     
        ![İşlev ana bilgisayar anahtar kodu](media/indexing-encrypted-blob-files/function-host-key.jpg "Azure Işlevi ana bilgisayar anahtar kodu nerede bulunur")

### <a name="cognitive-services"></a>Bilişsel Hizmetler

AI zenginleştirme ve beceri yürütmesi, doğal dil ve görüntü işleme için Metin Analizi ve Görüntü İşleme dahil bilişsel hizmetler tarafından desteklenir. Amacınız gerçek bir prototipi veya projeyi tamamlayacaksa, bu noktada bilişsel hizmetler sağlama (Azure Bilişsel Arama ile aynı bölgede), böylece dizin oluşturma işlemlerine iliştirebilirsiniz.

Bununla birlikte, Azure Bilişsel Arama, arka planda bilişsel hizmetlere bağlanıp Dizin Oluşturucu başına 20 ücretsiz işlem sunabileceğinden kaynak sağlamayı atlayabilirsiniz. 20 belge çalıştıktan sonra, bilişsel hizmetler anahtarı beceri 'ye iliştirilmediği takdirde Dizin Oluşturucu başarısız olur. Daha büyük projeler için, Kullandıkça öde, bu hizmetleri, Kullandıkça öde, bu hizmetler için sağlama bölümüne planlayın. Daha fazla bilgi için bkz. bilişsel [Hizmetler iliştirme](cognitive-search-attach-cognitive-services.md). Seçtiğiniz bilişsel yeteneklerin bilişsel hizmetler 'e (örneğin, buna bir yetenek eklenmediyse) bağlanmasa bile, en çok 20 ' den fazla belge içeren bir beceri çalıştırmak için bilişsel hizmetler anahtarının gerekli olduğunu unutmayın.

### <a name="azure-cognitive-search"></a>Azure Bilişsel Arama

Son bileşen, [portalda oluşturabileceğiniz](search-create-service-portal.md)Azure bilişsel arama. Bu kılavuzu gerçekleştirmek için ücretsiz katmanı kullanabilirsiniz. 

Azure Işlevinde olduğu gibi, yönetici anahtarını toplamak için bir dakikanızı ayırın. Ayrıca, istekleri raporlamaya başladığınızda, her bir isteğin kimliğini doğrulamak için kullanılan uç nokta ve yönetici API 'si anahtarını sağlamanız gerekir.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Bilişsel Arama yönelik bir yönetici API anahtarı ve URL 'SI alın

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetinize **genel bakış** sayfasında, arama hizmetinizin adını alın. Uç nokta URL 'sini inceleyerek hizmet adınızı doğrulayabilirsiniz. Uç nokta URL 'niz olsaydı `https://mydemo.search.windows.net` , hizmet adınız olur `mydemo` .

2. **Ayarlar**  >  **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

   ![Hizmet adı ve yönetici ve sorgu anahtarlarını alın](media/search-get-started-javascript/service-name-and-keys.png)

Tüm istekler, hizmetinize gönderilen her isteğin üstbilgisinde bir API anahtarı gerektirir. Geçerli bir anahtar, istek başına, isteği gönderen uygulama ve onu işleyen hizmet arasında güven oluşturur.

## <a name="2---set-up-postman"></a>2-Postman 'ı ayarlama

Postman yükleme ve ayarlama.

### <a name="download-and-install-postman"></a>Postman indirme ve yükleme

1. [Postman koleksiyonu kaynak kodunu](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json)indirin.
1.   >  Kaynak kodu Postman içine aktarmak için dosya **içeri aktarma** ' yı seçin.
1. **Koleksiyonlar** sekmesini seçin ve ardından **...** (üç nokta) düğmesini seçin.
1. **Düzenle**'yi seçin. 
   
   ![Gezintiyi gösteren Postman uygulaması](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Postman 'daki düzenleme menüsüne git")
1. **Düzenle** Iletişim kutusunda **değişkenler** sekmesini seçin. 

**Değişkenler** sekmesinde, Postman 'ın her bir çift küme içinde belirli bir değişkenle karşılaştığı her seferinde takas eden değerler ekleyebilirsiniz. Örneğin Postman, sembolü `{{admin-key}}` için ayarladığınız geçerli değerle değiştirir `admin-key` . Postman, URL 'Lerde, üst bilgilerde, istek gövdesinde vb. değişiklik yapar. 

Değerini almak için `admin-key` , daha önce not ettiğiniz Azure bilişsel arama admin API anahtarını kullanın. `search-service-name`Kullandığınız Azure bilişsel arama hizmetinin adına ayarlayın. `storage-connection-string`Depolama hesabınızın **erişim anahtarları** sekmesindeki değeri kullanarak ayarlayın ve `storage-container-name` şifrelenmiş dosyaların depolandığı depolama hesabındaki blob kapsayıcısının adına ayarlayın. `function-uri`Daha önce not ettiğiniz Azure Işlev URL 'sine ayarlayın ve daha `function-code` önce not ettiğiniz Azure işlevi ana bilgisayar anahtar koduna ayarlayın. Diğer değerler için varsayılan değerleri bırakabilirsiniz.

![Postman uygulama değişkenleri sekmesi](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Postman 'ın değişkenler penceresi")

| Değişken    | Nereden edinilir: |
|-------------|-----------------|
| `admin-key` | Azure Bilişsel Arama hizmetinin **anahtarlar** sayfasında.  |
| `search-service-name` | Azure Bilişsel Arama hizmetinin adı. URL `https://{{search-service-name}}.search.windows.net` . |
| `storage-connection-string` | Depolama hesabında, **erişim anahtarları** sekmesinde **KEY1**  >  **bağlantı dizesi**' ni seçin. |
| `storage-container-name` | Dizine eklenecek şifrelenmiş dosyaları olan blob kapsayıcısının adı. |
| `function-uri` |  Azure Işlevinde ana sayfada **Essentials** altında. |
| `function-code` | Azure Işlevinde, **uygulama anahtarlarına** giderek, **varsayılan** anahtarı göstermek ve değeri kopyalamak için öğesine tıklayın. |
| `api-version` | **2020-06-30** olarak bırakın. |
| `datasource-name` | **Şifreli-Bloblar** olarak bırakın. |
| `index-name` | **Şifrelenmiş-Bloblar-idx** olarak bırakın. |
| `skillset-name` | **Şifreli blob 'lar-sn** olarak bırakın. |
| `indexer-name` | **Şifreli Bloblar-ixR** olarak bırakın. |

### <a name="review-the-request-collection-in-postman"></a>Postman 'da istek koleksiyonunu gözden geçirin

Bu kılavuzu çalıştırdığınızda dört HTTP isteği vermelisiniz:

- **Dizini oluşturmak için Isteği yerleştir**: Bu dizin, Azure bilişsel arama tarafından kullanılan ve döndürdüğü verileri tutar.
- **Veri kaynağını oluşturmak Için post isteği**: Bu veri kaynağı, Azure bilişsel arama hizmetinizi depolama hesabınıza ve bu nedenle şifrelenmiş blob dosyalarına bağlar. 
- **Beceri oluşturma isteği**: Beceri, Azure işlevi için blob dosya verilerinin şifresini çözmek üzere özel yetenek tanımını ve şifresi çözülememiş olduktan sonra metnin her bir belgeden ayıklanmasına yönelik bir [Belgetextractionbeceri](cognitive-search-skill-document-extraction.md) belirler.
- **Dizin oluşturucuyu oluşturma Isteği koy**: Dizin oluşturucuyu çalıştırmak, verileri okur, Beceri uygular ve sonuçları depolar. Bu isteği en son çalıştırmanız gerekir.

[Kaynak kodu](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) , dört istek ve bazı yararlı izleme istekleri Içeren bir Postman koleksiyonu içerir. İstekleri vermek için, Postman 'da, istekler için sekmeyi seçin ve her biri için **Gönder** ' i seçin.

## <a name="3---monitor-indexing"></a>3-dizin oluşturmayı izleme

Dizin oluşturma ve zenginleştirme, oluşturma Dizin Oluşturucu isteğini gönderdikten hemen sonra. Depolama hesabınızda kaç belge olduğuna bağlı olarak, dizin oluşturma biraz zaman alabilir. Dizin oluşturucunun hala çalışır durumda olup olmadığını öğrenmek için Postman koleksiyonunun bir parçası olarak sunulan **Dizin Oluşturucu durumunu Al** isteğini kullanın ve dizin oluşturucunun çalışıp çalışmadığını öğrenmek veya hata ve uyarı bilgilerini görüntülemek için yanıtı gözden geçirin.  

Ücretsiz katmanı kullanıyorsanız şu ileti beklenir: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` . Bu ileti, boş katmandaki blob dizinlemesi, [karakter ayıklamaya karşı bir 32K sınırına](search-limits-quotas-capacity.md#indexer-limits)sahip olduğundan görüntülenir. Bu ileti, daha yüksek katmanlarda bu veri kümesi için görmezsiniz. 

## <a name="4---search"></a>4-ara

Dizin Oluşturucu yürütme tamamlandıktan sonra, verilerin başarıyla şifresinin çözülmüş ve dizine alınmış olduğunu doğrulamak için bazı sorgular çalıştırabilirsiniz. Portalda Azure Bilişsel Arama hizmetinize gidin ve [Arama Gezgini](search-explorer.md) 'ni kullanarak dizini oluşturulmuş veriler üzerinde sorgular çalıştırın.

## <a name="next-steps"></a>Sonraki adımlar

Şifrelenmiş dosyaları başarıyla dizinleolduğunuza göre, daha fazla bilişsel [beceriler ekleyerek bu](cognitive-search-defining-skillset.md)işlem hattını yineleyebilirsiniz. Bu, verilerinize yönelik ek Öngörüler elde etmeniz ve bunları zenginleştirmenize olanak tanır.

Şüpheli şifrelenmiş verilerle çalışıyorsanız, Azure Bilişsel Arama 'de kullanılabilen Dizin şifreleme özelliklerini araştırmak isteyebilirsiniz. Dizin oluşturucunun dizin oluşturma amacıyla şifresinin çözülmesi gerekir, ancak dizin mevcut olduğunda, müşteri tarafından yönetilen anahtar kullanılarak şifrelenebilir. Bu, bekleyen durumlarda verilerinizin her zaman şifrelenmesini güvence altına alacak. Daha fazla bilgi için bkz. [Azure bilişsel arama 'de veri şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırma](search-security-manage-encryption-keys.md).