---
title: 'Öğretici: faturalar-form tanıyıcıyı analiz etmek için Azure Logic Apps ile form tanıyıcı kullanma'
titleSuffix: Azure Cognitive Services
description: Bu öğreticide, bir modeli eğitme ve örnek verileri kullanarak test etme sürecini otomatikleştiren bir iş akışı oluşturmak için Azure Logic Apps ile form tanıyıcı kullanacaksınız.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 10/27/2019
ms.author: nitinme
ms.openlocfilehash: bf6efbdbe19be28c005e2081c99827734ef10174
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177002"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Öğretici: faturaları çözümlemek için Azure Logic Apps ile form tanıyıcı kullanma

Bu öğreticide, faturalardan verileri ayıklamak için Azure bilişsel hizmetler Suite 'in bir parçası olan form tanıyıcısı 'nı kullanan Azure Logic Apps bir iş akışı oluşturursunuz. Form tanıyıcıyı, örnek veri kümesi kullanarak bir modeli eğitmeniz ve ardından modeli başka bir veri kümesi kullanarak test etmek için kullanırsınız. Bu öğreticide kullanılan örnek veriler Azure Storage blob kapsayıcıları 'nda depolanır.

Bu öğreticinin şu şekilde ele alınmaktadır:

> [!div class="checklist"]
> * Form tanıyıcı için erişim isteyin
> * Azure Storage blob kapsayıcısı oluşturma
> * Örnek verileri Azure Blob kapsayıcısına yükleme
> * Azure mantıksal uygulaması oluşturma
> * Mantıksal uygulamayı form tanıyıcı kaynağı kullanacak şekilde yapılandırma
> * Mantıksal uygulamayı çalıştırarak iş akışını test etme

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/).

## <a name="request-access-for-form-recognizer"></a>Form tanıyıcı için erişim isteyin

Form tanıyıcı, sınırlı erişim önizlemesinde kullanılabilir. Önizlemeye erişim sağlamak için [form tanıyıcı erişim isteği](https://aka.ms/FormRecognizerRequestAccess) formunu doldurun ve gönderebilirsiniz. İsteğiniz Azure bilişsel hizmetler ekibi tarafından onaylandıktan sonra, hizmete erişmek için yönergeler içeren bir e-posta alacaksınız.

## <a name="understand-the-invoice-to-be-analyzed"></a>Çözümlenecek faturayı anlayın

Modeli eğitme ve modeli test etmek için kullandığımız örnek veri kümesi [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451)'dan bir. zip dosyası olarak kullanılabilir. . Zip dosyasını indirip ayıklayın ve **/tren** klasörü altında BIR fatura PDF dosyası açın. Fatura numarası, fatura tarihi vb. içeren bir tablo olduğunu fark edin. 

> [!div class="mx-imgBorder"]
> ![örnek fatura](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

Bu öğreticide, Azure Logic Apps ve form tanıyıcı kullanılarak oluşturulan bir iş akışını kullanarak bu tablodaki bilgileri JSON biçimine nasıl ayıklayacağınızı öğreneceksiniz.

## <a name="create-an-azure-storage-blob-container"></a>Azure Storage blob kapsayıcısı oluşturma

Bu kapsayıcıyı kullanarak modeli eğitebilmeniz için gereken örnek verileri karşıya yükleyebilirsiniz.

1. Depolama hesabı oluşturmak için [Azure depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md) bölümündeki yönergeleri izleyin. Depolama hesabı adı olarak **formdepostorage** kullanın.
1. Azure depolama hesabı içinde kapsayıcı oluşturmak için [Azure Blob kapsayıcısı oluşturma](../../storage/blobs/storage-quickstart-blobs-portal.md) bölümündeki yönergeleri izleyin. Kapsayıcı adı olarak **formncontainer** kullanın. Ortak erişim düzeyini kapsayıcı olarak ayarladığınızdan emin olun **(kapsayıcılar ve Bloblar için anonim okuma erişimi)** .

    > [!div class="mx-imgBorder"]
    > ![blob kapsayıcısı oluşturma](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Örnek verileri Azure Blob kapsayıcısına yükleme

[GitHub](https://go.microsoft.com/fwlink/?linkid=2090451)'da bulunan örnek verileri indirin. Verileri yerel bir klasöre ayıklayın ve **/tren** klasörünün içeriğini daha önce oluşturduğunuz **formbir kapsayıcıya** yükleyin. Bir kapsayıcıya veri yüklemek için [Blok Blobu yükleme](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) bölümündeki yönergeleri izleyin.

Kapsayıcının URL 'sini kopyalayın. Bu öğreticide daha sonra ihtiyacınız olacaktır. Depolama hesabı ve kapsayıcıyı bu öğreticide listelenen aynı adlarla oluşturduysanız, URL *https:\//formrecostorage.blob.Core.Windows.net/formrecocontainer/* olur.

## <a name="create-a-form-recognizer-resource"></a>Form tanıyıcı kaynağı oluşturma

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Mantıksal uygulamanızı oluşturma

Görevleri ve iş akışlarını otomatikleştirebilmek ve düzenlemek için Azure Logic Apps kullanabilirsiniz. Bu öğreticide, e-posta eki olarak çözümlemek istediğiniz bir fatura alarak tetiklenen bir mantıksal uygulama oluşturursunuz. Bu iş akışında aşağıdaki görevleri gerçekleştirirsiniz:
* Bir faturaya iliştirilmiş bir e-posta aldığınızda mantıksal uygulamayı otomatik olarak tetikleyecek şekilde yapılandırın.
* Azure Blob depolama alanına yüklediğiniz örnek verileri kullanarak bir modeli eğitme amacıyla mantıksal uygulamayı bir form tanıyıcısı **eğitme modeli** işlemi kullanacak şekilde yapılandırın.
* Mantıksal uygulamayı, zaten eğitilen modeli kullanmak için bir form tanıyıcı **formu analiz etme** işlemini kullanacak şekilde yapılandırın. Bu bileşen, daha önce eğitilen modele göre bu mantıksal uygulamaya sağladığınız faturayı analiz edecektir.

Haydi başlayalım! İş akışınızı ayarlamak için bu adımları izleyin.

1. Ana Azure menüsünden **kaynak oluştur** > **tümleştirme** > **mantıksal uygulama**' yı seçin.

1. **Mantıksal uygulama oluştur** bölümünde, mantıksal uygulamanızın ayrıntılarını burada gösterildiği gibi sağlayın. İşiniz bittiğinde **Oluştur**' u seçin.

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Adı** | <*Logic-App-adı*> | Mantıksal uygulamanızın adı, yalnızca harf, sayı, kısa çizgi (`-`), alt çizgi (`_`), parantezler (`(`, `)`) ve nokta (`.`) içerebilir. Bu örnek "My-First-Logic-App" kullanır. |
   | **Abonelik** | <*Azure-subscription-name*> | Azure abonelik adınız |
   | **Kaynak grubu** | <*Azure-Resource-Group-name*> | İlgili kaynakları düzenlemek için kullanılan [Azure Kaynak grubunun](./../../azure-resource-manager/resource-group-overview.md) adı. Bu örnek "My-First-LA-RG" kullanır. |
   | **Konum** | <*Azure-bölge*> | Mantıksal uygulama bilgilerinizin depolanacağı bölge. Bu örnek, "Batı ABD" kullanır. |
   | **Log Analytics** | Kapalı | Tanılama günlüğüne kaydetme ayarını **Kapalı** durumda bırakın. |
   ||||

1. Azure 'un uygulamanızı dağıtmasından sonra Azure araç çubuğunda **bildirimler** >  dağıtılan mantıksal uygulamanız Için**Kaynağa Git** ' i seçin. Ya da, arama kutusuna adı yazarak mantıksal uygulamanızı bulabilir ve seçebilirsiniz.

   Logic Apps Tasarımcısı açılır ve bir tanıtım videosu ile sık kullanılan tetikleyicilerin bulunduğu bir sayfa görüntülenir. **Şablonlar** altında **Boş Mantıksal Uygulama**'yı seçin.

   > [!div class="mx-imgBorder"]
   > ![mantıksal uygulama için boş şablon seçin](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>Bir e-posta geldiğinde mantıksal uygulamayı iş akışını tetikleyecek şekilde yapılandırma

Bu öğreticide, ekli fatura ile bir e-posta alındığında iş akışını tetiklersiniz. Bu öğretici için, e-posta hizmeti olarak Office 365 ' i seçeceğiz, ancak kullanmak istediğiniz başka bir e-posta sağlayıcısını kullanabilirsiniz.

1. Sekmelerde, tümü ' nü seçin, **Office 365 Outlook**' u seçin ve ardından **Tetikleyiciler**altında **Yeni bir e-posta geldiğinde**öğesini seçin.

    ![Gelen bir e-posta aracılığıyla mantıksal uygulamayı tetikleme](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. **Office 365 Outlook** kutusunda **oturum aç**' a tıklayın ve Office 365 hesabında oturum açmak için ayrıntıları girin.

1. Sonraki iletişim kutusunda aşağıdaki adımları gerçekleştirin.
    1. Yeni e-postalar için izlenecek klasörü seçin.
    1. **Ekler** için **Evet**' i seçin. Bu, yalnızca ekleri olan e-postaların iş akışını tetiklenmesini sağlar.
    1. **Ekleme ekleri** için **Evet**' i seçin. Bu, ek içeriğinin aşağı akış işlemede kullanılmasını sağlar.

        > [!div class="mx-imgBorder"]
        > mantıksal uygulama e-posta tetikleyicisini yapılandırma ![](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Üstteki araç çubuğundan **Kaydet** ' e tıklayın.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Mantıksal uygulamayı form tanıyıcı eğitme modeli işlemini kullanacak şekilde yapılandırma

Faturaları çözümlemek için form tanıyıcı hizmetini kullanabilmeniz için önce modelin çözümleyebilecekleri ve öğrenebilecekleri bazı örnek faturaların verilerini sağlayarak bir modeli eğmeniz gerekir.

1. **Yeni adım**' ı seçin ve **Eylem Seç**' in altında **form tanıyıcısı**' nı arayın. Görüntülenen sonuçlardan, **form tanıyıcı**' i seçin ve ardından Form tanıyıcı için kullanılabilen eylemler altında **modeli eğitme**' yi seçin.

    > [!div class="mx-imgBorder"]
    > Form tanıyıcı modeli ![eğitme](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Form tanıyıcı iletişim kutusunda bağlantı için bir ad girin ve uç nokta URL 'sini ve form tanıyıcı kaynağı için aldığınız anahtarı girin.

    > [!div class="mx-imgBorder"]
    > Form tanıyıcı için bağlantı adı ![](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    **Oluştur**’a tıklayın.

1. **Modeli eğitme** iletişim kutusunda, **kaynak**için örnek verileri karşıya yüklediğiniz kapsayıcının URL 'sini girin.

    > [!div class="mx-imgBorder"]
    > örnek faturalar için ![depolama kapsayıcısı](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Üstteki araç çubuğundan **Kaydet** ' e tıklayın.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Logic App formunu, tanıyıcı formu çözümle işlemini kullanacak şekilde yapılandırma

Bu bölümde, iş akışına **formu çözümle** işlemini eklersiniz. Bu işlem, mantıksal uygulamaya sunulan yeni bir faturayı çözümlemek için zaten eğitilen modeli kullanır.

1. **Yeni adım**' ı seçin ve **Eylem Seç**' in altında **form tanıyıcısı**' nı arayın. Görüntülenen sonuçlardan, **form tanıyıcı**' i seçin ve ardından Form tanıyıcı için kullanılabilen eylemler altında, **analiz formu**' nu seçin.

    > [!div class="mx-imgBorder"]
    > Form tanıyıcı modelini analiz ![](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. **Formu çözümle** iletişim kutusunda, şunları yapın:

    1. **Model kimliği** metin kutusuna tıklayın ve açılan iletişim kutusunda, **dinamik Içerik** sekmesinde **ModelId**' yi seçin. Bunu yaparak, Flow uygulamasına son bölümde eğitilen modelin model KIMLIĞINI sağlarsınız.

        > [!div class="mx-imgBorder"]
        > ![form tanıyıcı için ModelId kullanın](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. **Belge** metin kutusuna tıklayın ve açılan iletişim kutusunda, **dinamik Içerik** sekmesinde, **ekler içerik**' i seçin. Bunu yaparak akışı, iş akışını tetiklemek için gönderilen e-postaya eklenen örnek fatura dosyasını kullanacak şekilde yapılandırırsınız.

        > [!div class="mx-imgBorder"]
        > ![faturaları çözümlemek için e-posta ekini kullanın](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Üstteki araç çubuğundan **Kaydet** ' e tıklayın.

### <a name="extract-the-table-information-from-the-invoice"></a>Faturadan tablo bilgilerini ayıklayın

Bu bölümde, mantıksal uygulamayı faturalar içindeki tablodaki bilgileri ayıklamak için yapılandıracağız.

1. **Eylem Ekle**' yi seçin ve **Eylem Seç**' in altında, **Oluştur** ' u arayın ve kullanılabilir Eylemler altında, yeniden **Oluştur** ' u seçin.
    Fatura](media/tutorial-form-recognizer-with-logic-apps/extract-table.png) tablo bilgilerini ![Ayıkla

1. **Oluştur** Iletişim kutusunda **girişler** metin kutusuna tıklayın ve açılan iletişim kutusundan **Tablolar**' ı seçin.

    > [!div class="mx-imgBorder"]
    > Fatura](media/tutorial-form-recognizer-with-logic-apps/select-tables.png) tablo bilgilerini ![Ayıkla

1. **Kaydet** düğmesine tıklayın.

## <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

Mantıksal uygulamayı test etmek için [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451)'dan indirdiğiniz örnek veri kümesinin **/Test** klasöründeki örnek faturaları kullanın. Aşağıdaki adımları uygulayın:

1. Uygulamanızın Azure Logic Apps tasarımcısında, üstteki araç çubuğundan **Çalıştır** ' ı seçin. İş akışı artık etkin olur ve faturaya eklenmiş bir e-posta almayı bekler.
1. Mantıksal uygulamayı oluştururken girdiğiniz e-posta adresine eklenmiş örnek bir faturaya sahip bir e-posta gönderin. E-postanın mantıksal uygulamayı yapılandırırken verdiğiniz klasöre teslim edildiğinden emin olun.
1. E-posta klasöre teslim edildiğinde, Logic Apps tasarımcı her aşamanın ilerlemesini içeren bir ekran gösterir. Aşağıdaki ekran görüntüsünde, ek içeren bir e-postanın alındığını ve iş akışının devam ettiğini görürsünüz.

    > [!div class="mx-imgBorder"]
    > ![bir e-posta göndererek iş akışını başlatın](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. İş akışının tüm aşamaları çalışmayı tamamladığında Logic Apps tasarımcı her aşamada yeşil bir onay kutusu gösterir. Tasarımcı penceresinde **her 2 için**öğesini seçin ve ardından **Oluştur**' u seçin.

    > [!div class="mx-imgBorder"]
    > ![Iş akışı tamamlandı](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    **Çıktılar** kutusunda, çıktıyı kopyalayın ve herhangi bir metin düzenleyicisine yapıştırın.

1. JSON çıkışını e-postada ek olarak gönderdiğiniz örnek faturayla karşılaştırın. JSON verilerinin faturadaki tablodaki verilere karşılık geldiğini doğrulayın.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    Bu öğreticiyi başarıyla tamamladınız!

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir modeli eğitmek ve bir faturanın içeriğini ayıklamak için form tanıyıcı kullanmak üzere bir Azure Logic Apps iş akışı ayarlarsınız. Daha sonra, kendi formlarınıza benzer bir senaryo oluşturabilmeniz için eğitim veri kümesi oluşturmayı öğrenin.

> [!div class="nextstepaction"]
> [Eğitim veri kümesi oluşturma](build-training-data-set.md)