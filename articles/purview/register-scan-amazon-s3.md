---
title: Amazon S3 demetlerini tarama
description: Bu nasıl yapılır kılavuzunda, Amazon S3 demetlerini taramanın ayrıntıları açıklanmaktadır.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/21/2021
ms.custom: references_regions
ms.openlocfilehash: 75a7cba1e47509e3186ab519d0d8ca82dd315373
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815531"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Amazon için Azure purview Bağlayıcısı S3

Bu nasıl yapılır Kılavuzu, şu anda Amazon S3 standart demetlerine depolanmış yapılandırılmamış verilerinizi taramak ve verilerinizde ne tür hassas bilgilerin bulunduğunu öğrenmek için Azure purview 'ın nasıl kullanılacağına ilişkin bir açıklama sağlar. Bu nasıl yapılır Kılavuzu, verilerin şu anda kolay bilgi koruması ve veri uyumluluğu için depolandığı Amazon S3 demetlerini nasıl tanımlayabileceğinizi de açıklamaktadır.

Bu hizmet için, purview tarayıcısının çalışacağı AWS 'ye güvenli erişim sağlayan bir Microsoft hesabı sağlamak için purview kullanın. Purview tarayıcısı, verilerinizi okumak için bu erişimi Amazon S3 demetlerine kullanır ve ardından yalnızca meta veriler ve sınıflandırma dahil olmak üzere tarama sonuçlarını Azure 'a geri bildirir. Veri tarama sonuçlarınızı çözümlemek ve gözden geçirmek için, Izleme sınıflandırması ve etiketleme raporlarını kullanın.

Bu nasıl yapılır kılavuzunda, Amazon S3 demetlerini adım adım kaynak olarak ekleme ve Amazon S3 verileriniz için bir tarama oluşturma hakkında bilgi edineceksiniz.

## <a name="purview-scope-for-amazon-s3"></a>Amazon S3 için purview kapsamı

Aşağıdaki kapsam,, Amazon S3 demetlerini takip eden veri kaynakları olarak kaydetme ve tarama için özeldir.

|Kapsam  |Açıklama  |
|---------|---------|
|**Veri sınırları**     |    Purview tarayıcı hizmeti şu anda, kiracı başına 100 GB veri için Amazon S3 demetlerini taramayı desteklemektedir.     |
|**Dosya türleri**     | Purview tarayıcı hizmeti şu anda aşağıdaki dosya türlerini desteklemektedir: <br><br>. avro,. csv,. doc,. docm,. docx,. dot,. JSON,. odp,. ods,. odt,. Orc,. Parquet,. PDF,. pot,. PPS,. ppsx,. ppt,. pptm,. pptx,. PSV,. SSV,. tsv,. txt,. xlc,. xls,. xlsb,. xlsm,. xlsx,. xlt,. xml        |
|**Bölgeler**     | Amazon S3 hizmetine yönelik purview Bağlayıcısı Şu anda yalnızca **AWS ABD Doğu (Ohio)** ve **Avrupa (Frankfurt)** bölgelerinde dağıtılır. <br><br>Daha fazla bilgi için bkz. [depolama ve Tarama bölgeleri](#storage-and-scanning-regions).   |
|     |         |

Daha fazla bilgi için şu adreste belgelenen takip görünümü sınırlarına bakın:

- [Azure purview ile kaynaklar için kotaları yönetme ve artırma](how-to-manage-quotas.md)
- [Azure purview 'da desteklenen veri kaynakları ve dosya türleri](sources-and-scans.md)
- [Purview hesabınız için özel uç noktaları kullanın](catalog-private-link.md)
### <a name="storage-and-scanning-regions"></a>Depolama ve Tarama bölgeleri

Aşağıdaki tabloda, verileri Azure purview tarafından taranacak bölgede depoladığınız bölgeler eşlenir.

> [!IMPORTANT]
> Müşteriler, demet bölgesinin bölgesine göre ilgili tüm veri aktarımı ücretleri üzerinden ücretlendirilir.
>

| Depolama bölgesi | Tarama bölgesi |
| ------------------------------- | ------------------------------------- |
| ABD Doğu (Ohio)                  | ABD Doğu (Ohio)                        |
| ABD Doğu (N. Virginia           | ABD Doğu (N. Virginia                       |
| ABD Batı (N. For         | ABD Doğu (Ohio)                        |
| ABD Batı (Oregon)                | ABD Doğu (Ohio)                        |
| Afrika (Cabo Town)              | Avrupa (Frankfurt)                    |
| Asya Pasifik (Hong Kong)        | Asya Pasifik (Sidney)                   |
| Asya Pasifik (Mumbai)           | Asya Pasifik (Sidney)                   |
| Asya Pasifik (Osaka-yerel)      | Asya Pasifik (Sidney)                   |
| Asya Pasifik (Seoul)            | Asya Pasifik (Sidney)                   |
| Asya Pasifik (Singapur)        | Asya Pasifik (Sidney)                   |
| Asya Pasifik (Sidney)           | Asya Pasifik (Sidney)                  |
| Asya Pasifik (Tokyo)            | Asya Pasifik (Sidney)                 |
| Kanada (orta)                | ABD Doğu (Ohio)                        |
| Çin (Pekin)                 | Desteklenmez                    |
| Çin (Ningxia)                 | Desteklenmez                   |
| Avrupa (Frankfurt)              | Avrupa (Frankfurt)                    |
| Avrupa (Irlanda)                | Avrupa (Irlanda)                   |
| Avrupa (Londra)                 | Avrupa (Irlanda)                   |
| Avrupa (MILAN)                  | Avrupa (Frankfurt)                    |
| Avrupa (Paris)                  | Avrupa (Frankfurt)                    |
| Avrupa (Stockholm)              | Avrupa (Frankfurt)                    |
| Orta Doğu (Bahreyn)           | Avrupa (Frankfurt)                    |
| Güney Amerika (Sao Paulo)       | ABD Doğu (Ohio)                        |
| | |

## <a name="prerequisites"></a>Önkoşullar

Amazon S3 demetlerinizi eklemek ve S3 verilerinizi taramak için aşağıdaki önkoşulları gerçekleştirdiğinizden emin olun.

> [!div class="checklist"]
> * Azure purview veri kaynağı Yöneticisi olmanız gerekir.
> * Henüz bir [hesabınız yoksa bir takip hesabı oluşturun](#create-a-purview-account)
> * [AWS demet tarıyorsanız için bir takip bilgisi oluşturun](#create-a-purview-credential-for-your-aws-bucket-scan)
> * [Purview ile kullanmak için yeni bir AWS rolü oluşturma](#create-a-new-aws-role-for-purview)
> * Uygunsa, [şifrelenmiş Amazon S3 demetlerini taramayı yapılandırma](#configure-scanning-for-encrypted-amazon-s3-buckets)
> * Demetlerinizi takip eden kaynak olarak eklerken [AWS ARN](#retrieve-your-new-role-arn), [demet adınızın](#retrieve-your-amazon-s3-bucket-name)ve bazen [AWS hesap kimliğiniz](#locate-your-aws-account-id)değerlerinin olması gerekir.

### <a name="create-a-purview-account"></a>Bir purview hesabı oluşturun

- **Zaten bir takip hesabınız varsa,** AWS S3 desteği için gereken yapılandırmalara devam edebilirsiniz. [AWS demeti taramanıza yönelik bir purview kimlik bilgisi oluşturma](#create-a-purview-credential-for-your-aws-bucket-scan)ile başlayın.

- **Bir purview hesabı oluşturmanız gerekiyorsa,** [Azure purview hesabı örneği oluşturma](create-catalog-portal.md)' daki yönergeleri izleyin. Hesabınızı oluşturduktan sonra, yapılandırmayı tamamladıktan sonra, Amazon S3 için purview bağlayıcısını kullanmaya başlamak için buraya dönün.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>AWS demet tarıyorsanız için bir takip bilgisi oluşturun

Bu yordamda, AWS demetlerinizi tararken kullanılacak yeni bir purview kimlik bilgisinin nasıl oluşturulacağı açıklanmaktadır.

> [!TIP]
> Ayrıca, [taramanızı yapılandırırken](#create-a-scan-for-one-or-more-amazon-s3-buckets)işlemin ortasında yeni bir kimlik bilgisi oluşturabilirsiniz. Bu durumda, **kimlik bilgileri** alanında **Yeni**' yi seçin.
>

1. Purview **yönetim merkezine** gidin ve **güvenlik ve erişim** altında **kimlik bilgileri**' ni seçin.

1. **Yeni**' yi seçin ve sağ tarafta görüntülenen **yeni kimlik bilgisi** bölmesinde aşağıdaki alanları kullanarak purview kimlik bilgilerinizi oluşturun:

    |Alan |Açıklama  |
    |---------|---------|
    |**Ad**     |Bu kimlik bilgisi için anlamlı bir ad girin veya varsayılanı kullanın.        |
    |**Açıklama**     |Bu kimlik bilgisi için isteğe bağlı bir açıklama girin, örneğin `Used to scan the tutorial S3 buckets`         |
    |**Kimlik doğrulama yöntemi**     |Kovaya erişim için bir rol kullandığınızdan bu yana bir **rol ARN** seçin.         |
    |**Microsoft hesabı KIMLIĞI**     |Bu değeri panoya kopyalamak için tıklayın. [AWS 'de rolünüzü oluştururken](#create-a-new-aws-role-for-purview) **Microsoft hesabı kimliği** olarak bu değeri kullanın.           |
    |**Dış KIMLIK**     |Bu değeri panoya kopyalamak için tıklayın. [AWS 'de rolünüzü oluştururken](#create-a-new-aws-role-for-purview) bu DEĞERI **dış kimlik** olarak kullanın.        |
    |**Rol ARN**     | [Amazon IAM rolünüzü](#create-a-new-aws-role-for-purview)oluşturduktan sonra, IAM alanında rolünüze gidin, **rol ARN** değerini kopyalayın ve buraya girin. Örneğin: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Daha fazla bilgi için bkz. [Yeni rolünüzü alma ARN](#retrieve-your-new-role-arn). |
    | | |

    Kimlik bilgisini oluşturmayı bitirmeyi bitirdiğinizde **Oluştur** ' u seçin.

1. Henüz yapmadıysanız, bir sonraki adımınız olan [purview için yeni BIR AWS rolü oluştururken](#create-a-new-aws-role-for-purview)kullanmak üzere **Microsoft hesabı KIMLIĞI** ve **dış kimlik** değerlerini kopyalayıp yapıştırın.

Purview kimlik bilgileri hakkında daha fazla bilgi için bkz. [Azure purview 'da kaynak kimlik doğrulaması Için kimlik bilgileri](manage-credentials.md).

### <a name="create-a-new-aws-role-for-purview"></a>Purview için yeni bir AWS rolü oluşturma

Bu yordam, AWS rolünüzü oluştururken Azure hesap KIMLIĞINIZ ve dış KIMLIĞINIZ için değerleri girmenizi gerektirir.

Bu değerlere sahip değilseniz, bunları önce [purview kimlik bilgilerinizin](#create-a-purview-credential-for-your-aws-bucket-scan)içinde bulun.

**Microsoft HESABı kimliğinizi ve dış kimliğinizi bulmak için**:

1. Purview 'da **Yönetim Merkezi**  >  **güvenlik ve erişim**  >  **kimlik bilgilerine** gidin.

1. [AWS demet taramanızı için oluşturduğunuz](#create-a-purview-credential-for-your-aws-bucket-scan)kimlik bilgisini seçin ve ardından araç çubuğunda **Düzenle**' yi seçin.

1. Sağ tarafta görünen **kimlik bilgisini düzenle** BÖLMESINDE **Microsoft hesabı KIMLIĞI** ve **dış kimlik** değerlerini ayrı bir dosyaya kopyalayın ya da AWS 'deki ilgili alana yapıştırma için yararlı olmasını isteyin.

    Örnek:

    [![MICROSOFT HESABı kimliğinizi ve dış kimlik değerlerini bulun. ](./media/register-scan-amazon-s3/locate-account-id-external-id.png)](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


**Takip görünümü için AWS rolünüzü oluşturmak için**:

1.  **Amazon Web Services** konsolunuzu açın ve **güvenlik, kimlik ve uyumluluk** altında **IAM**' i seçin.

1. **Roller** ' i ve ardından **rol oluştur**' u seçin.

1. **Başka BIR AWS hesabı** seçin ve ardından aşağıdaki değerleri girin:

    |Alan  |Açıklama  |
    |---------|---------|
    |**Hesap Kimliği**     |    Microsoft hesabı KIMLIĞINIZI girin. Örnek: `615019938638`     |
    |**Dış KIMLIK**     |   Seçenekler altında **dış kimlik iste...**' yi seçin ve ardından, atanan alana dış kimliğinizi girin. <br>Örnek: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    Örnek:

    ![AWS hesabınıza Microsoft hesap KIMLIĞI ekleyin.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. **Rol oluştur > izin Ilkeleri Ekle** alanında, **S3**' e görünen izinleri filtreleyin. **AmazonS3ReadOnlyAccess** öğesini seçin ve ardından **İleri: Etiketler**' i seçin.

    ![Yeni Amazon S3 tarama rolü için ReadOnlyAccess ilkesini seçin.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > **AmazonS3ReadOnlyAccess** Ilkesi, S3 demetlerinizi taramak için gereken en düşük izinleri sağlar ve diğer izinleri de içerebilir.
    >
    >Yalnızca demetlerinizi taramak için gereken en düşük izinleri uygulamak için, [AWS Ilkenizin en düşük izinlerinde](#minimum-permissions-for-your-aws-policy)listelenen izinlerle, tek bir demet veya hesabınızdaki tüm demetleri taramak isteyip istemediğinize bağlı olarak yeni bir ilke oluşturun. 
    >
    >Yeni ilkenizi AmazonS3ReadOnlyAccess yerine role uygulayın **.**

1. **Etiketler Ekle (isteğe bağlı)** alanında, isteğe bağlı olarak bu yeni rol için anlamlı bir etiket oluşturmayı seçebilirsiniz. Faydalı Etiketler, oluşturduğunuz her bir rol için erişimi düzenlemenizi, izlemenizi ve denetlemenize olanak tanır.

    Etiketinize gereken şekilde yeni bir anahtar ve değer girin. İşiniz bittiğinde veya bu adımı atlamak istiyorsanız, Ileri ' yi seçin. rol ayrıntılarını gözden geçirmek için **gözden geçirin** ve rol oluşturmayı doldurun.

    ![Yeni rolünüzün erişimini düzenlemek, izlemek veya denetlemek için anlamlı bir etiket ekleyin.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. **İnceleme** alanında, şunları yapın:

    - **Rol adı** alanına, rolünüz için anlamlı bir ad girin
    - **Rol açıklaması** kutusuna rolün amacını tanımlamak için isteğe bağlı bir açıklama girin
    - **İlkeler** bölümünde, role doğru Ilkenin (**AmazonS3ReadOnlyAccess**) eklendiğinden emin olun.

    Ardından, işlemi gerçekleştirmek için **rol oluştur** ' u seçin.

    Örnek:

    ![Rolünüzü oluşturmadan önce ayrıntıları gözden geçirin.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Şifrelenmiş Amazon S3 demetlerini taramayı yapılandırma

AWS demetleri birden çok şifreleme türünü destekler. **AWS-KMS** şifrelemesini kullanan demetler için taramayı etkinleştirmek için özel yapılandırma gerekir.

> [!NOTE]
> Şifreleme, AES-256 veya AWS-KMS S3 şifrelemesi kullanan demetler için, bu bölümü atlayın ve [Amazon S3 demet adınızı almaya](#retrieve-your-amazon-s3-bucket-name)devam edin.
>

**Amazon S3 demet'larınız için kullanılan şifreleme türünü denetlemek için:**

1. AWS 'de, **depolama**  >  **S3** > gidin ve soldaki menüden **demetler** ' i seçin.

    ![Amazon S3 demetleri sekmesini seçin.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Denetlemek istediğiniz demetini seçin. Demet 'in ayrıntılar sayfasında, **Özellikler** sekmesini seçin ve **varsayılan şifreleme** alanına gidin.

    - Seçtiğiniz demet herhangi bir şey için yapılandırılmışsa (örneğin,  demet için varsayılan şifreleme **devre dışıysa**), bu yordamın geri kalanını atlayın ve [Amazon S3 demet adınızı almaya](#retrieve-your-amazon-s3-bucket-name)devam edin.

    - Seçtiğiniz demet **AWS-KMS** şifrelemesi için yapılandırıldıysa, özel **AWS-KMS** şifrelemesiyle demet taramaya izin veren yeni bir ilke eklemek için aşağıda açıklanan şekilde devam edin.

    Örnek:

    ![AWS-KMS şifrelemesi ile yapılandırılmış bir Amazon S3 demeti görüntüleme](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Özel AWS-KMS şifrelemesiyle demet taramaya izin vermek üzere yeni bir ilke eklemek için:**

1. AWS 'de, **Hizmetler**  >   **IAM**  >   **ilkeleri**' ne gidin ve **ilke oluştur**' u seçin.

1. **İlke**  >  **görsel Düzenleyicisi** Oluştur sekmesinde, ilkenizi aşağıdaki değerlerle tanımlayın:

    |Alan  |Açıklama  |
    |---------|---------|
    |**Hizmet**     |  **KMS** girin ve seçin.       |
    |**Eylemler**     | **Yazma** bölümünü genişletmek için **erişim düzeyi** altında **yaz** ' ı seçin.<br>Genişlettikten sonra yalnızca **şifre çözme** seçeneğini belirleyin.        |
    |**Kaynaklar**     |Belirli bir kaynağı veya **tüm kaynakları** seçin.         |
    | | |

    İşiniz bittiğinde devam etmek için **Ilkeyi gözden geçir** ' i seçin.

    ![AWS-KMS şifrelemesi ile demeti taramak için bir ilke oluşturun.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. **Ilkeyi gözden geçir** sayfasında, ilkeniz için anlamlı bir ad ve isteğe bağlı olarak bir açıklama girin ve ardından **ilke oluştur**' u seçin.

    Yeni oluşturulan ilke, ilke listenize eklenir.

1. Yeni ilkenizi, tarama için eklediğiniz role ekleyin.

    1. **IAM**  >  **rolleri** sayfasına geri gidin ve [daha önce](#create-a-new-aws-role-for-purview)eklediğiniz rolü seçin.

    1. **İzinler** sekmesinde, **ilke Ekle**' yi seçin.

        ![Rolün Izinler sekmesinde, ilke Ekle ' yi seçin.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. **Izinleri Ekle** sayfasında, yukarıda oluşturduğunuz yeni ilkeyi arayıp seçin. İlkenizi role eklemek için **Ekle ilke** ' yi seçin.

        **Özet** sayfası, yeni ilkenize ekli olarak güncelleştirilir.

        ![Rolünüze yeni ilke eklenmiş olan güncelleştirilmiş bir Özet sayfasını görüntüleyin.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Yeni rolünüzü alma ARN

[Amazon S3 demet için bir tarama oluştururken](#create-a-scan-for-one-or-more-amazon-s3-buckets)AWS ROLÜNÜZÜ ARN olarak kaydetmeniz ve bunu takip edilecek şekilde kopyalamanız gerekir.

**Rolünüzü almak için:**

1. AWS **kimlik ve erişim yönetimi (IAM)**  >  **rolleri** alanında, [purview için oluşturduğunuz](#create-a-purview-credential-for-your-aws-bucket-scan)yeni rolü arayın ve seçin.

1. Rolün **Özet** sayfasında, **rol ARN** değerinin sağında **Panoya Kopyala** düğmesini seçin.

    ![Rol ARN değerini panoya kopyalayın.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Bu değeri güvenli bir konuma yapıştırın ve bu, [Amazon S3 demet için bir tarama oluştururken](#create-a-scan-for-one-or-more-amazon-s3-buckets)kullanmaya hazırlanın.

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Amazon S3 demet adınızı alın

Amazon S3 [demet için bir tarama oluştururken](#create-a-scan-for-one-or-more-amazon-s3-buckets) bu dosyayı takip etmek Için Amazon S3 demet adınızın adı gerekir

**Demet adınızı almak için:**

1. AWS 'de, **depolama**  >  **S3** > gidin ve soldaki menüden **demetler** ' i seçin.

    ![Amazon S3 demetleri sekmesini görüntüleyin.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Demet ayrıntıları sayfasını görüntülemek için demetini arayıp seçin ve ardından demet adını panoya kopyalayın.

    Örnek:

    ![S3 demet URL 'sini alın ve kopyalayın.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Demet adınızı güvenli bir dosyaya yapıştırın ve `s3://` bu değere bir ön ek ekleyerek, demetini bir purview kaynağı olarak yapılandırırken girmeniz gereken değeri oluşturun.

    Örnek: `s3://purview-tutorial-bucket`

> [!NOTE]
> Bir takip görünümü veri kaynağı olarak yalnızca demet 'nizin kök düzeyi desteklenir. Örneğin, bir alt klasör içeren aşağıdaki *URL desteklenmez:*`s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>AWS hesap KIMLIĞINIZI bulun

AWS hesabınızı, tüm demetlerinin yanı sıra bir takip görünümü veri kaynağı olarak kaydetmek için AWS hesap KIMLIĞINIZ gerekir.

AWS hesap KIMLIĞINIZ AWS konsolunda oturum açmak için kullandığınız KIMLIĞIDIR. Bu panoyu, ıAM panosunda oturum açtıktan sonra, gezinti seçeneklerinin sol tarafında ve oturum açma URL 'nizin sayısal parçası olarak en üst kısımdaki bir kez daha bulabilirsiniz:

Örnek:

![AWS hesap KIMLIĞINIZI alın.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Bir purview kaynağı olarak tek bir Amazon S3 demeti ekleme

Bu yordamı, yalnızca bir veri kaynağı olarak takip etmek istediğiniz tek bir S3 demetini varsa veya AWS hesabınızda birden fazla demet varsa, ancak bunların tümünü purview 'a kaydetmek istemiyorsanız bu yordamı kullanın.

**Demetini eklemek için**: 

1. Amazon S3 URL için adanmış purview bağlayıcısını kullanarak purview portalını başlatın. Bu URL, Amazon S3 purview Connector ürün yönetimi ekibi tarafından size sağlandı.

    ![Purview portalını başlatın.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Azure purview **kaynakları** sayfasına gidin ve kaydı Kaydet simgesini seçin  ![ .](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Devam edin**.

    ![Bir Amazon AWS demetini bir purview veri kaynağı olarak ekleyin.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Birden çok [koleksiyonunuz](manage-data-sources.md#manage-collections) varsa ve Amazon S3 ' i belirli bir koleksiyona eklemek istiyorsanız, sağ üstteki **harita görünümünü** seçin ve ardından **kaydı kaydet** ![ simgesini seçin.](./media/register-scan-amazon-s3/register-button.png) düğmesine basın.
    >

1. Açılan **Kaynakları Kaydet (Amazon S3)** bölmesinde, aşağıdaki ayrıntıları girin:

    |Alan  |Açıklama  |
    |---------|---------|
    |**Ad**     |Anlamlı bir ad girin veya belirtilen varsayılanı kullanın.         |
    |**Demet URL 'SI**     | Aşağıdaki sözdizimini kullanarak AWS demet URL 'nizi girin:   `s3://<bucketName>`     <br><br>**Note**: hiçbir alt klasör olmadan demet 'nizin yalnızca kök düzeyini kullandığınızdan emin olun. Daha fazla bilgi için bkz. [Amazon S3 demet adınızı alma](#retrieve-your-amazon-s3-bucket-name). |
    |**Bir koleksiyon seçin** |Bir veri kaynağını bir koleksiyonun içinden kaydetmeyi seçtiyseniz, bu koleksiyon zaten listelenir. <br><br>Gerektiğinde farklı bir koleksiyon seçin, hiçbir koleksiyon atamamak için **hiçbiri** veya yeni bir koleksiyon oluşturmak için **Yeni** ' ye tıklayın. <br><br>Purview koleksiyonları hakkında daha fazla bilgi için bkz. [Azure purview 'da veri kaynaklarını yönetme](manage-data-sources.md#manage-collections).|
    | | |

    İşiniz bittiğinde kayıt işleminin tamamlanması için **son** ' u seçin.

Bir [veya daha fazla Amazon S3 demetleri için tarama oluşturma](#create-a-scan-for-one-or-more-amazon-s3-buckets)ile devam edin.

## <a name="add-an-amazon-account-as-a-purview-resource"></a>Bir Amazon hesabını bir purview kaynağı olarak ekleme

Amazon hesabınızda birden çok S3 demeti varsa ve bunların tümünü purview veri kaynakları olarak kaydetmek istiyorsanız bu yordamı kullanın.

[Taramanızı yapılandırırken](#create-a-scan-for-one-or-more-amazon-s3-buckets), bunların tümünü birlikte taramak istemiyorsanız taramak istediğiniz belirli demetleri seçebileceksiniz.

**Amazon hesabınızı eklemek için**:
1. Amazon S3 URL için adanmış purview bağlayıcısını kullanarak purview portalını başlatın. Bu URL, Amazon S3 purview Connector ürün yönetimi ekibi tarafından size sağlandı.

    ![Amazon için başlatma Bağlayıcısı S3 adanmış takip görünümü portalı](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Azure purview **kaynakları** sayfasına gidin ve kaydı Kaydet simgesini seçin  ![ .](./media/register-scan-amazon-s3/register-button.png) > **Amazon hesapları**  >  **Devam edin**.

    ![Bir Amazon hesabını bir purview veri kaynağı olarak ekleyin.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Birden çok [koleksiyonunuz](manage-data-sources.md#manage-collections) varsa ve Amazon S3 ' i belirli bir koleksiyona eklemek istiyorsanız, sağ üstteki **harita görünümünü** seçin ve ardından **kaydı kaydet** ![ simgesini seçin.](./media/register-scan-amazon-s3/register-button.png) düğmesine basın.
    >

1. Açılan **Kaynakları Kaydet (Amazon S3)** bölmesinde, aşağıdaki ayrıntıları girin:

    |Alan  |Açıklama  |
    |---------|---------|
    |**Ad**     |Anlamlı bir ad girin veya belirtilen varsayılanı kullanın.         |
    |**AWS hesap KIMLIĞI**     | AWS hesap KIMLIĞINIZI girin. Daha fazla bilgi için bkz. [AWS hesap kimliğinizi bulma](#locate-your-aws-account-id)|
    |**Bir koleksiyon seçin** |Bir veri kaynağını bir koleksiyonun içinden kaydetmeyi seçtiyseniz, bu koleksiyon zaten listelenir. <br><br>Gerektiğinde farklı bir koleksiyon seçin, hiçbir koleksiyon atamamak için **hiçbiri** veya yeni bir koleksiyon oluşturmak için **Yeni** ' ye tıklayın. <br><br>Purview koleksiyonları hakkında daha fazla bilgi için bkz. [Azure purview 'da veri kaynaklarını yönetme](manage-data-sources.md#manage-collections).|
    | | |

    İşiniz bittiğinde kayıt işleminin tamamlanması için **son** ' u seçin.

Bir [veya daha fazla Amazon S3 demetleri için tarama oluşturma](#create-a-scan-for-one-or-more-amazon-s3-buckets)ile devam edin.

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>Bir veya daha fazla Amazon S3 demetleri için tarama oluşturma

Demetlerinizi takip etme veri kaynakları olarak ekledikten sonra, bir taramayı zamanlanmış aralıklarla veya anında çalışacak şekilde yapılandırabilirsiniz.

1. Azure purview **kaynakları** alanına gidin ve aşağıdakilerden birini yapın:

    - **Harita görünümünde** **Yeni tarama** ![ Yeni tarama simgesini seçin.](./media/register-scan-amazon-s3/new-scan-button.png) veri kaynağı kutusunda.
    - **Liste görünümünde**, veri kaynağınız için satırın üzerine gelin ve yeni **tarama** ![ Yeni tarama simgesini seçin. ](./media/register-scan-amazon-s3/new-scan-button.png)

1. Sağ tarafta açılan **tarama...** bölmesinde aşağıdaki alanları tanımlayın ve ardından **devam**' ı seçin:

    |Alan  |Açıklama  |
    |---------|---------|
    |**Ad**     |  Tarama için anlamlı bir ad girin veya varsayılanı kullanın.       |
    |**Tür** |Yalnızca AWS hesabınızı eklediyseniz, tüm demetlerin dahil edildiğini görürsünüz. <br><br>Geçerli seçenekler yalnızca **Tüm**  >  **Amazon S3**' i içerir. Takip görünümü 'nin destek matrisi genişledikçe seçilecek daha fazla seçenek için ayarlanmış kalın. |
    |**Kimlik Bilgisi**     |  Rolünüzün adıyla bir takip eden kimlik bilgisi seçin. <br><br>**İpucu**: Şu anda yeni bir kimlik bilgisi oluşturmak istiyorsanız **Yeni**' yi seçin. Daha fazla bilgi için bkz. [AWS demeti taramanıza yönelik bir takip kimlik bilgisi oluşturma](#create-a-purview-credential-for-your-aws-bucket-scan).     |
    | **Amazon S3**    |   Yalnızca AWS hesabınızı eklediyseniz, tüm demetlerin dahil edildiğini görürsünüz. <br><br>Taranacak bir veya daha fazla demet seçin ya da hesabınızdaki tüm demetleri taramak için tümü ' nü **seçin** .      |
    | | |

    Purview, ARN 'ın geçerli olduğunu ve demetlerin içindeki demetlerin ve nesnelerin erişilebilir olduğunu denetler ve bağlantı başarılı olursa devam eder.

    > [!TIP]
    > Devam etmeden önce farklı değerler girmek ve bağlantıyı kendinize test etmek için **devam**' ı seçmeden önce sağ alt kısımdaki **Bağlantıyı Sına** ' yı seçin.
    >

1. **Bir tarama kuralı kümesi seçin** bölmesinde, **AmazonS3** varsayılan kural kümesini seçin ya da yeni bir özel kural kümesi oluşturmak için **Yeni tarama kuralı ayarla** ' yı seçin. Kural kümesini seçtikten sonra **devam**' ı seçin.

    Yeni bir özel tarama kuralı kümesi oluşturmayı seçerseniz, aşağıdaki ayarları tanımlamak için Sihirbazı kullanın:

    |Bölme  |Description  |
    |---------|---------|
    |**Yeni tarama kuralı kümesi** /<br>**Tarama kuralı açıklaması**    |   Kural kümesi için anlamlı bir ad ve isteğe bağlı bir açıklama girin      |
    |**Dosya türlerini seçin**     | Taramaya dahil etmek istediğiniz tüm dosya türlerini seçin ve ardından **devam**' ı seçin.<br><br>Yeni dosya türü eklemek için **yeni dosya türü**' nü seçin ve aşağıdakileri tanımlayın: <br>-Eklemek istediğiniz dosya uzantısı <br>-İsteğe bağlı bir açıklama  <br>-Dosya içeriğinin özel bir sınırlayıcısı olup olmadığı veya bir sistem dosyası türü olup olmadığı. Ardından, özel sınırlayıcıyı girin veya sistem dosya türünü seçin. <br><br>Özel dosya türünü oluşturmak için **Oluştur** ' u seçin.     |
    |**Sınıflandırma kurallarını seçin**     |   ' A gidin ve veri kümeniz üzerinde çalıştırmak istediğiniz sınıflandırma kurallarını seçin.      |
    |     |         |

    Kural kümesini oluşturmak için işiniz bittiğinde **Oluştur** ' u seçin.

1. **Tarama tetikleyicisi ayarla** bölmesinde, aşağıdakilerden birini seçin ve ardından **devam**' ı seçin:

    - Yinelenen bir tarama için zamanlama yapılandırmak üzere **yineleniyor**
    - Hemen başlayan bir taramayı **yapılandırmak için**

1. **Taramayı gözden geçirin** bölmesinde, doğru olduğunu doğrulamak için tarama ayrıntılarınızı denetleyin ve ardından önceki bölmede **Kaydet** veya  **Kaydet ve Çalıştır** ' ı seçin.

    > [!NOTE]
    > Çalışmaya başladıktan sonra, taramanın tamamlanması 24 saate kadar sürebilir. Her taramayı başlattıktan sonra, **Insight raporlarınızı** gözden geçirebilir ve kataloğu 24 saat arayabileceksiniz.
    >

Daha fazla bilgi için bkz. [purview tarama sonuçlarını araştırma](#explore-purview-scanning-results).

## <a name="explore-purview-scanning-results"></a>Takip görünümü tarama sonuçlarını keşfet

Amazon S3 demet'larınızda bir takip görünümü taraması tamamlandıktan sonra, tarama geçmişini görüntülemek için takip **kaynakları** alanında ayrıntıya gidin.

Ayrıntılarını görüntülemek için bir veri kaynağı seçin ve ardından, çalışmakta olan veya tamamlanmış taramaları görüntülemek için **taramalar** sekmesini seçin.
Birden çok demet içeren bir AWS hesabı eklediyseniz, her demet için tarama geçmişi hesap altında gösterilir.

Örnek:

![AWS S3 demet taramalarını AWS hesap kaynağınızın altına gösterin.](./media/register-scan-amazon-s3/account-scan-history.png)

Amazon S3 demetleri dahil olmak üzere, verilerinizde bulunan içerikle ilgili ayrıntıları öğrenmek için purview 'ın diğer bölgelerini kullanın:

- **Purview veri kataloğunu arayın** ve belirli bir demet için filtre uygulayın. Örnek:

    ![AWS S3 varlıkları için katalogda arama yapın.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- Sınıflandırma, duyarlılık etiketleri, dosya türleri ve içeriğiniz hakkında daha fazla ayrıntı için istatistikleri görüntülemek üzere **öngörü raporlarını görüntüleyin** .

    Tüm purview Insight Reports, Azure veri kaynaklarınızdaki sonuçların geri kalanında birlikte, Amazon S3 tarama sonuçlarını içerir. İlgili olduğunda, rapor filtreleme seçeneklerine ek bir **Amazon S3** varlık türü eklenmiştir.

    Daha fazla bilgi için bkz. [Azure purview 'Ta öngörüleri anlama](concept-insights.md).

## <a name="minimum-permissions-for-your-aws-policy"></a>AWS ilkeniz için en düşük izinler

S3 demetlerinizi tararken kullanılacak [BIR AWS rolü oluşturmak](#create-a-new-aws-role-for-purview) için varsayılan yordam, **AmazonS3ReadOnlyAccess** ilkesini kullanır.

**AmazonS3ReadOnlyAccess** Ilkesi, S3 demetlerinizi taramak için gereken en düşük izinleri sağlar ve diğer izinleri de içerebilir.

Yalnızca demetlerinizi taramak için gereken en düşük izinleri uygulamak için, aşağıdaki bölümlerde listelenen izinlere sahip yeni bir ilke oluşturun ve bu durumda, hesabınızdaki tek bir demet veya tüm demetleri taramak isteyip istemediğiniz bir ilke oluşturun.

Yeni ilkenizi AmazonS3ReadOnlyAccess yerine role uygulayın **.**

### <a name="individual-buckets"></a>Bireysel demetler

Tek bir S3 demetleri taranırken, en düşük AWS izinleri şunları içerir:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

Kaynağı belirli bir demet adıyla tanımlamadığınızdan emin olun. Örnek:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>Hesabınızdaki tüm demetler

AWS hesabınızdaki tüm demetler taranırken, en düşük AWS izinleri şunları içerir:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

Kaynağınızı bir joker karakterle tanımlamadığınızdan emin olun. Örnek:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Azure purview Insight Reports hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Azure Purview'da İçgörüleri Anlama](concept-insights.md)
