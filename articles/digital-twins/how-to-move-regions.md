---
title: Örneği farklı bir Azure bölgesine taşıma
titleSuffix: Azure Digital Twins
description: Azure dijital TWINS örneğini bir Azure bölgesinden diğerine taşıma bölümüne bakın.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 62db56ac9791cea7d6f1a40f794241ed68fa90fa
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483590"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Azure dijital TWINS örneğini farklı bir Azure bölgesine taşıma

Azure dijital TWINS örneğinizi bir bölgeden diğerine taşımanız gerekiyorsa, geçerli işlem kaynaklarınızın yeni bölgede yeniden oluşturulması ve ardından orijinal kaynakları silmesi olacaktır. Bu işlemin sonunda, güncelleştirilmiş konum dışında, ilki ile aynı olan yeni bir Azure dijital TWINS örneğiyle çalışmaya başlayacaksınız.

Bu makalede, yeni örneğin orijinalden eşleşmesi için ihtiyacınız olan her şeyi nasıl taşıyacağınız ve kopyalamanın nasıl yapılacağı hakkında rehberlik sunulmaktadır.

Bu işlem aşağıdaki adımları içerir:

1. Hazırlama: özgün modellerinizi, TWINS 'i ve grafiği Indirin.
1. Taşı: yeni bir bölgede yeni bir Azure dijital TWINS örneği oluşturun.
1. Taşı: yeni Azure dijital TWINS örneğini yeniden doldurmak.
    - Özgün modelleri, TWINS 'i ve grafiği karşıya yükleyin.
    - Uç noktaları ve yolları yeniden oluşturun.
    - Bağlı kaynakları yeniden bağlayın.
1. Kaynak kaynaklarını Temizleme: özgün örneği silin.

## <a name="prerequisites"></a>Önkoşullar

Azure dijital TWINS örneğinizi yeniden oluşturmayı denemeden önce, yeniden oluşturulması gereken tüm parçaların net bir fikrini almak için özgün örneğinizin bileşenlerine gidin.

Dikkate alınacak bazı sorular şunlardır:

* Örneğim ne tür *modeller* karşıya yüklendi? Kaç tane var?
* Örneğimde *TWINS* nedir? Kaç tane var?
* Örneðimde *grafiğin* genel şekli nedir? Kaç ilişki var?
* Örneğimde hangi *uç noktalara* sahibim?
* Örneğimde hangi *yollara* sahibim? Filtreler mi var?
* Örnek, *diğer Azure hizmetlerine nereden bağlanır*? Bazı ortak tümleştirme noktaları şunları içerir:

    - Azure Event Grid, Azure Event Hubs veya Azure Service Bus
    - Azure İşlevleri
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Haritalar
    - Azure IoT Hub Cihazı Sağlama Hizmeti
* Örneğime hangi *kişisel veya Şirket uygulamaları* bağlanmalıyım?

Bu bilgileri [Azure Portal](https://portal.azure.com), [Azure dijital TWINS API 'leri ve SDK](how-to-use-apis-sdks.md)'Ları, [Azure dijital TWINS CLI komutları](how-to-use-cli.md)veya [Azure Digital TWINS Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) örneğini kullanarak toplayabilirsiniz.

## <a name="prepare"></a>Hazırlama

Bu bölümde, özgün modellerinizi, TWINS 'i ve grafiği özgün örnekten indirerek örneğinizi yeniden oluşturmaya hazırlanabilirsiniz. Bu makalede, bu görev için [Azure Digital TWINS gezgin](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) örneği kullanılmaktadır.

>[!NOTE]
>Zaten modellerinizi veya örneğinizin grafiğini içeren dosyalarınız olabilir. Bu durumda, her şeyi yeniden indirmeniz gerekmez; yalnızca eksik olan veya bu dosyaları özgün olarak karşıya yüklediğiniz sırada değişmiş olabilecek şeyler. Örneğin, yeni verilerle güncelleştirilmiş TWINS olabilir.

### <a name="limitations-of-azure-digital-twins-explorer"></a>Azure Digital TWINS Explorer 'ın sınırlamaları

[Azure Digital TWINS Explorer örneği](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) , grafiğinizin görsel gösterimini destekleyen ve örneğiniz ile görsel etkileşim sağlayan bir istemci uygulaması örneğidir. Bu makalede, modellerinizi, TWINS 'leri ve grafiklerinizi indirmek ve daha sonra yeniden yüklemek için nasıl kullanılacağı gösterilmektedir.

Bu örnek, tüm bir araç değildir. Yük testi değil, büyük boyuttaki grafikleri işlemek için derlenmedi. Sonuç olarak, aşağıdaki hazır olmayan örnek sınırlamaları göz önünde bulundurun:

* Örnek şu anda yalnızca 1.000 düğüme ve 2.000 ilişkiye kadar grafik boyutlarında test edilmiştir.
* Örnek, aralıklı hatalarda yeniden denemeyi desteklemez.
* Karşıya yüklenen veriler tamamlanmamışsa, örnek kullanıcıya bildirimde bulunmayacaktır.
* Örnek, bellek gibi kullanılabilir kaynakları aşan çok büyük grafiklerden kaynaklanan hataları işlemez.

Örnek, grafiğinizin boyutunu işleyemezse, diğer Azure dijital TWINS geliştirici araçlarını kullanarak grafiği dışarı aktarıp içeri aktarabilirsiniz:

* [Azure dijital TWINS CLı komutları](how-to-use-cli.md)
* [Azure dijital TWINS API 'Leri ve SDK 'ları](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>Azure Digital TWINS gezgin uygulamasını ayarlama

Azure dijital TWINS Gezgini ile devam etmek için, önce örnek uygulama kodunu indirin ve makinenizde çalışacak şekilde ayarlayın.

Örneği almak için [Azure Digital Twins Gezgini](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)' ne gidin. Başlığın altındaki **kodu görüntüle** düğmesini seçin, bu, sizi örnek için GitHub deposuna götürür. Örnek olarak indirmek için **kod** düğmesini SEÇIN ve **ZIP 'i indirin** *. ZIP* dosyasını makinenize.

:::image type="content" source="media/how-to-move-regions/download-repo-zip.png" alt-text="GitHub 'da dijital-TWINS-Explorer deposunun ekran görüntüsü. Kod düğmesi seçilidir ve posta Indirme düğmesinin vurgulandığı küçük bir iletişim kutusu oluşturulur." lightbox="media/how-to-move-regions/download-repo-zip.png":::

Dosyanın sıkıştırmasını açın.

Sonra, Azure Digital TWINS Explorer için izinleri ayarlayın ve yapılandırın. Azure dijital TWINS hızlı başlangıç konusunun [Azure dijital TWINS ve Azure Digital TWINS Explorer 'ı ayarlama](quickstart-azure-digital-twins-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) bölümündeki yönergeleri izleyin. Bu bölümde aşağıdaki adımlarda izlenecek yol gösterilmektedir:

1. Bir Azure dijital TWINS örneği ayarlayın. Zaten bir örneğiniz olduğundan, bu bölümü atlayabilirsiniz.
1. Örneğinize erişim sağlamak için yerel Azure kimlik bilgilerini ayarlayın.
1. Azure dijital TWINS Gezginini çalıştırın ve örneğinizi örneğine bağlanacak şekilde yapılandırın. Taşıdığınız orijinal Azure dijital TWINS örneğinizin *ana bilgisayar adını* kullanacaksınız.

Şimdi, makinenizde bir tarayıcıda çalışan Azure Digital TWINS gezgin örnek uygulamasına sahip olmanız gerekir. Örnek, özgün Azure dijital TWINS örneğinize bağlanmalıdır.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Localhost: 3000 konumunda çalışan bir uygulamayı gösteren tarayıcı penceresi. Uygulamaya Azure dijital TWINS Gezgini adı verilir ve sorgu Gezgini, model görünümü, grafik görünümü ve Özellik Gezgini için kutular bulunur. Henüz ekran verisi yok." lightbox="media/how-to-move-regions/explorer-blank.png":::

Bağlantıyı doğrulamak için, **Graph Explorer** kutusundaki grafikteki tüm TWINS ve ilişkileri görüntüleyen varsayılan sorguyu çalıştırmak üzere **Sorguyu Çalıştır** düğmesini seçin.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Pencerenin sağ üst köşesinde bir düğme okuma Çalıştır sorgusu vurgulanır." lightbox="media/how-to-move-regions/run-query.png":::

Bu öğeleri hedef bölgede yeni örneğinize yeniden yüklemek için bu makalenin ilerleyen kısımlarında yer alan Azure dijital TWINS Gezginini çalışır durumda bırakabilirsiniz.

### <a name="download-models-twins-and-graph"></a>Model, TWINS ve Graf 'yi indirin

Sonra, çözümünüzdeki modelleri, TWINS 'i ve grafiği makinenize indirin.

Bu öğelerin tümünü aynı anda indirmek için, önce grafik **görünümü** kutusunda tam grafiğin göründüğünden emin olun. Tam grafik zaten görünmezse, `SELECT * FROM digitaltwins` **sorgu Gezgini** kutusunda varsayılan sorgusunu yeniden çalıştırın.
 
Sonra **GRAFIK görünümü** kutusunda **grafiği dışarı aktar** simgesini seçin.

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Grafik görünümü kutusunda bir simge vurgulanır. Bir bulutun dışına işaret eden bir ok gösterir." lightbox="media/how-to-move-regions/export-graph.png":::

Bu eylem, **GRAFIK görünümü** kutusunda bir **indirme** bağlantısı sunar. Modellerinizi, TWINS 'leri ve ilişkileri içeren sorgu sonucunun JSON tabanlı temsilini indirmek için bunu seçin. Bu eylem, makinenize bir. JSON dosyası indirmelidir.

>[!NOTE]
>İndirilen dosyanın farklı bir dosya uzantısı varsa, uzantıyı doğrudan düzenlemenizi ve. JSON olarak değiştirmeyi deneyin.

## <a name="move"></a>Taşı

Daha sonra, hedef bölgede yeni bir örnek oluşturarak örneğinizin "taşıma" bölümünü tamamlayacaksınız. Daha sonra bu dosyayı özgün örneğinizin verileri ve bileşenleriyle dolduracaksınız.

### <a name="create-a-new-instance"></a>Yeni örnek oluştur

İlk olarak, hedef bölgenizde Azure dijital TWINS 'in yeni bir örneğini oluşturun. [Nasıl yapılır: örnek ve kimlik doğrulaması ayarlama](how-to-set-up-instance-portal.md)bölümündeki adımları izleyin. Bu işaretçileri göz önünde bulundurun:

* Farklı bir kaynak grubunda yer *alıyorsa* , yeni örnek için aynı adı koruyabilirsiniz. Özgün örneğinizi içeren aynı kaynak grubunu kullanmanız gerekiyorsa, yeni örneğiniz kendi farklı adına sahip olur.
* Konum sorulduğunda yeni hedef bölgeyi girin.

Bu adım tamamlandıktan sonra, verilerinizi verilerle ayarlamaya devam etmek için yeni örneğinizin ana bilgisayar adına sahip olmanız gerekir. Kurulum sırasında konak adını görmüyorsanız, Azure portal hemen almak için [Bu yönergeleri](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) izleyin.

### <a name="repopulate-the-old-instance"></a>Eski örneği yeniden doldurmak

Ardından, yeni örneği, özgün kopyanın bir kopyası olacak şekilde ayarlayacaksınız.

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>Azure dijital TWINS gezginini kullanarak özgün modelleri, TWINS 'leri ve grafiği karşıya yükleme

Bu bölümde, modellerinizi, TWINS 'i ve grafiği yeni örneğe yeniden yükleyebilirsiniz. Özgün Örneğinizde herhangi bir model, TWINS veya grafik yoksa veya yeni örneğe taşımak istemiyorsanız, bir [sonraki bölüme](#re-create-endpoints-and-routes)atlayabilirsiniz.

Aksi takdirde, Azure Digital TWINS Explorer çalıştıran tarayıcı penceresine dönün ve aşağıdaki adımları izleyin.

##### <a name="connect-to-the-new-instance"></a>Yeni örneğe Bağlan

Şu anda Azure dijital TWINS Gezgini, özgün Azure dijital TWINS örneğinizi bağlamış. Pencerenin sağ üst köşesindeki **oturum aç** düğmesini seçerek bağlantıyı yeni örneğinizi işaret etmek üzere değiştirin.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Pencerenin sağ üst köşesinde bulunan oturum aç simgesini vurgulayan Azure dijital TWINS Gezgini. Simge, bir anahtarın silueti içeren bir kişinin basit silueti gösterir." lightbox="media/how-to-move-regions/sign-in.png":::

**ADT URL 'sini** yeni örneğinizi yansıtacak şekilde değiştirin. Bu değeri *https://{yeni örnek ana bilgisayar adı}* olarak okunacak şekilde değiştirin.

**Bağlan**’ı seçin. Azure kimlik bilgilerinizle yeniden oturum açmanız veya örneğiniz için bu uygulamaya izin vermeniz istenebilir.

##### <a name="upload-models-twins-and-graph"></a>Model, TWINS ve Graf yükleme

Ardından, daha önce indirdiğiniz çözüm bileşenlerini yeni örneğinize yükleyin.

Modellerinizi, TWINS 'i ve Graf 'yi karşıya yüklemek için grafik **görünümü** kutusunda **Graf al** simgesini seçin. Bu seçenek, bu bileşenlerin üçünü aynı anda karşıya yükler. Bu, şu anda grafikte kullanılmayan modelleri de karşıya yükler.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Grafik görünümü kutusunda bir simge vurgulanır. Bir buluta işaret eden bir ok gösterir." lightbox="media/how-to-move-regions/import-graph.png":::

Dosya Seçicisi kutusunda, indirdiğiniz grafiğinize gidin. Graph **. JSON** dosyasını seçin ve **Aç**' ı seçin.

Birkaç saniye sonra Azure Digital TWINS Explorer, yüklenecek Grafiğin önizlemesini gösteren bir **Içeri aktarma** görünümü açar.

Grafik karşıya yüklemeyi onaylamak için **GRAFIK görünümü** kutusunun sağ üst köşesindeki **Kaydet** simgesini seçin.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Grafik Önizleme bölmesindeki Kaydet simgesini vurgulama." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Azure dijital TWINS Gezgini artık modellerinizi ve Graf (TWINS ve ilişkiler dahil) yeni Azure dijital TWINS örneğinizi karşıya yükler. Kaç tane model, TWINS ve ilişki yüklendiğini gösteren bir başarı iletisi görmeniz gerekir.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Graph import başarısını belirten iletişim kutusu. ' Içeri aktarma başarılı ' i okur. 2 model içeri aktarıldı. 4 TWINS içeri aktarıldı. 2 ilişki içeri aktarıldı. '" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Her şeyin başarıyla karşıya yüklendiğini doğrulamak için Graph **Explorer** kutusunda **Sorguyu Çalıştır** düğmesini seçerek grafikteki tüm TWINS ve ilişkileri görüntüleyen varsayılan sorguyu çalıştırın. Bu eylem ayrıca **model görünümü** kutusundaki model listesini de yeniler.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Pencerenin sağ üst köşesindeki Sorguyu Çalıştır düğmesinin etrafında vurgulayın." lightbox="media/how-to-move-regions/run-query.png":::

Grafınızı **GRAFIK Gezgini** kutusunda görünen tüm TWINS ve ilişkilerle görmeniz gerekir. **Model görünümü** kutusunda modellerinizi de görmeniz gerekir.

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Model görünümü kutusunda vurgulanan iki modeli gösteren Azure dijital TWINS Gezgini görünümü ve grafik Gezgini kutusunda vurgulanmış bir grafik." lightbox="media/how-to-move-regions/post-upload.png":::

Bu görünümler, modellerinizin, TWINS 'in ve grafiğinin hedef bölgedeki yeni örneğe yeniden yüklendiğini onaylayın.

#### <a name="re-create-endpoints-and-routes"></a>Uç noktaları ve yolları yeniden oluşturma

Özgün örneğiniz için uç noktalar veya rotalar varsa, bunları yeni Örneğinizde yeniden oluşturmanız gerekir. Özgün Örneğinizde herhangi bir uç nokta veya yol yoksa veya yeni örneğe taşımak istemiyorsanız, [sonraki bölüme](#relink-connected-resources)atlayabilirsiniz.

Aksi takdirde, [nasıl yapılır: yeni örneği kullanarak uç noktaları ve yolları yönetme](how-to-manage-routes-portal.md) bölümündeki adımları izleyin. Bu işaretçileri göz önünde bulundurun:

* Uç nokta için kullanmakta olduğunuz Event Grid, Event Hubs veya Service Bus kaynağını yeniden *oluşturmanız gerekmez.* Daha fazla bilgi için uç nokta yönergelerindeki "Önkoşullar" bölümüne bakın. Uç noktayı Azure dijital TWINS örneğinde yeniden oluşturmanız yeterlidir.
* Farklı bir örnek kapsamında olduklarından bitiş noktası ve yol adlarını yeniden kullanabilirsiniz.
* Oluşturduğunuz yollara gerekli filtreleri eklemeyi unutmayın.

#### <a name="relink-connected-resources"></a>Bağlı kaynakları yeniden bağla

Özgün Azure dijital TWINS örneğinizle bağlantılı başka uygulamalarınız veya Azure kaynaklarınız varsa, bağlantıyı yeni örneğinize ulaşacak şekilde düzenlemeniz gerekir. Bu kaynaklar, Azure dijital TWINS ile çalışmak üzere ayarlamış olduğunuz diğer Azure hizmetlerini veya kişisel veya şirket uygulamalarını içerebilir.

Özgün örneğinizle bağlantılı başka kaynaklarınız yoksa veya yeni örneğe taşımak istemiyorsanız, bir [sonraki bölüme](#verify)atlayabilirsiniz.

Aksi takdirde, senaryonuza bağlı kaynakları göz önünde bulundurun. Bağlı kaynakları silip yeniden oluşturmanız gerekmez. Bunun yerine, yalnızca bir Azure dijital TWINS örneğine bağlandıkları noktaları, ana bilgisayar adıyla düzenlemeniz gerekir. Ardından bu noktaları, özgün yerine yeni örneğin ana bilgisayar adını kullanacak şekilde güncelleştirin.

Düzenlemeniz gereken tam kaynaklar senaryonuza bağlıdır, ancak bazı yaygın tümleştirme noktaları aşağıda verilmiştir:

* Azure Işlevleri. Kodu özgün örneğinin ana bilgisayar adını içeren bir Azure işleviniz varsa, bu değeri yeni örneğin ana bilgisayar adına güncelleştirmeniz ve işlevi yeniden yayımlamanız gerekir.
* Event Grid, Event Hubs veya Service Bus.
* Logic Apps.
* Time Series Insights.
* Azure haritalar.
* Cihaz sağlama hizmeti IoT Hub.
* Azure dışındaki kişisel veya Şirket uygulamaları (öğreticide oluşturulan istemci uygulaması gibi), örneğe bağlanıp Azure Digital TWINS API 'Lerini çağıran [bir istemci uygulamasını kodlayın](tutorial-code.md).
* Azure AD uygulama kayıtlarının yeniden *oluşturulması gerekmez.* Azure dijital TWINS API 'Lerine bağlanmak için bir [uygulama kaydı](how-to-create-app-registration.md) kullanıyorsanız, yeni örneğiniz ile aynı uygulama kaydını yeniden kullanabilirsiniz.

Bu adımı tamamladıktan sonra, hedef bölgedeki yeni örneğinizin özgün örneğin bir kopyası olması gerekir.

## <a name="verify"></a>Doğrulama

Yeni örneğinizin doğru şekilde ayarlandığını doğrulamak için aşağıdaki araçları kullanın:

* [Azure portalı](https://portal.azure.com). Portal, yeni örneğinizin var olduğunu ve doğru hedef bölgede olduğunu doğrulamak için uygundur. Ayrıca, uç noktaların ve yolların ve diğer Azure hizmetlerinin bağlantılarının doğrulanması da iyi bir uygulamadır.
* [Azure dijital TWINS CLI komutları](how-to-use-cli.md). Bu komutlar, yeni örneğinizin var olduğunu ve doğru hedef bölgede olduğunu doğrulamak için kullanışlıdır. Örnek verileri doğrulamak için de kullanılabilir.
* [Azure dijital TWINS Gezgini](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Azure dijital TWINS Gezgini, modeller, TWINS ve grafikler gibi örnek verileri doğrulamak için uygundur.
* [Azure dijital TWINS API 'leri ve SDK](how-to-use-apis-sdks.md)'ları. Bu kaynaklar modeller, TWINS ve grafikler gibi örnek verileri doğrulamak için uygundur. Bunlar, uç noktaların ve yolların doğrulanması için de uygundur.

Yeni örnekle doğru çalıştıklarından emin olmanıza yardımcı olması için, özgün örneğinizle çalıştırdığınız özel uygulamaları veya uçtan uca akışları çalıştırmayı da deneyebilirsiniz.

## <a name="clean-up-source-resources"></a>Kaynak kaynaklarını Temizleme

Yeni örneğiniz, özgün örneğin verilerinin ve bağlantılarının bir kopyasına sahip hedef bölgede ayarlandığına göre özgün örneği silebilirsiniz.

[Azure Portal](https://portal.azure.com), [Azure CLI](how-to-use-cli.md)veya [Denetim düzlemi API 'lerini](how-to-use-apis-sdks.md#overview-control-plane-apis)kullanabilirsiniz.

Azure portal kullanarak örneği silmek için, portalı bir tarayıcı penceresinde [açın](https://portal.azure.com) ve Portal arama çubuğunda adı arayarak özgün Azure dijital TWINS örneğinize gidin.

**Sil** düğmesini seçin ve silme işlemini tamamlaması için istemleri izleyin.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Genel Bakış sekmesinde Azure portal Azure dijital TWINS örneği ayrıntılarının görünümü. Sil düğmesi vurgulanır.":::