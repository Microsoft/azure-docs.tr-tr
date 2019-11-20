---
title: Azure Data Factory kullanarak SAP Business Warehouse 'tan veri yükleme
description: SAP Business Warehouse 'dan veri kopyalamak için Azure Data Factory kullanma (siyah beyaz)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: jingwang
ms.openlocfilehash: 0c96ecff27a57b3277e7c8105766059b739d11af
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672660"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Azure Data Factory kullanarak SAP Business Warehouse 'tan veri kopyalama

Bu makalede, Azure Data Factory kullanarak SAP Business Warehouse 'tan (bant genişliği) veri kopyalamak için Azure Data Lake Storage 2. hub 'a nasıl kullanılacağı gösterilmektedir. Verileri [desteklenen diğer havuz veri depolarına](copy-activity-overview.md#supported-data-stores-and-formats)kopyalamak için benzer bir işlem kullanabilirsiniz.

> [!TIP]
> Açık Hub tümleştirmesi ve değişim ayıklama akışı SAP BW dahil SAP BW verileri kopyalama hakkında genel bilgi için, bkz. [Azure Data Factory kullanarak SAP Business Warehouse 'Tan açık hub aracılığıyla veri kopyalama](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Ön koşullar

- **Azure Data Factory**: yoksa [bir veri fabrikası oluşturmak](quickstart-create-data-factory-portal.md#create-a-data-factory)için adımları izleyin.

- **Hedef türü "veritabanı tablosu" olan açık Merkez hedefi (OHD) SAP BW**: OHD oluşturmak için veya OHD 'nizin Data Factory tümleştirme için doğru yapılandırılıp yapılandırılmadığını kontrol etmek için, bu makalenin [SAP BW açık hub hedefi yapılandırması](#sap-bw-open-hub-destination-configurations) bölümüne bakın.

- **SAP BW Kullanıcı aşağıdaki izinlere ihtiyaç duyuyor**:

  - Uzak Işlev çağrıları (RFC) ve SAP BW için yetkilendirme.
  - **S_SDSAUTH** yetkilendirme nesnesinin "yürütme" etkinliğinin izinleri.

- **Sap .net bağlayıcı 3,0 ile [Şirket Içinde barındırılan bir tümleştirme çalışma zamanı (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime)** . Aşağıdaki kurulum adımlarını izleyin:

  1. Şirket içinde barındırılan tümleştirme çalışma zamanı, sürüm 3,13 veya üstünü yükleyip kaydettirin. (Bu işlem bu makalenin ilerleyen kısımlarında açıklanmaktadır.)

  2. [64-BIT SAP BAĞLAYıCıSıNı SAP web sitesinden Microsoft .NET 3,0 için](https://support.sap.com/en/product/connectors/msnet.html) indirin ve şirket IÇINDE barındırılan IR ile aynı bilgisayara yükleyin. Yükleme sırasında, aşağıdaki görüntüde gösterildiği gibi **Isteğe bağlı kurulum adımları** ILETIŞIM kutusunda **GAC 'ye derleme yükleme** ' yi seçtiğinizden emin olun:

     ![SAP .NET bağlayıcısını ayarlama iletişim kutusu](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>SAP BW açık hub 'dan tam bir kopya yapın

Azure portal veri fabrikanıza gidin. Data Factory Kullanıcı arabirimini ayrı bir sekmede açmak için **yazar & İzleyicisi** ' ni seçin.

1. **Başlarken** sayfasında, veri kopyalama aracını açmak için **veri kopyalama** ' yi seçin.

2. **Özellikler** sayfasında, bir **görev adı**belirtin ve ardından **İleri**' yi seçin.

3. **Kaynak veri deposu** sayfasında **+ Yeni bağlantı oluştur**' u seçin. Bağlayıcı galerisinden **Hub SAP BW açın** ve ardından **devam**' ı seçin. Bağlayıcıları filtrelemek için arama kutusuna **SAP** yazabilirsiniz.

4. **SAP BW hub bağlantısını seçin** sayfasında, yeni bir bağlantı oluşturmak için aşağıdaki adımları izleyin.

   ![Hub-bağlantılı hizmet sayfası oluştur SAP BW açın](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. **Tümleştirme çalışma zamanı aracılığıyla Bağlan** listesinden, mevcut bir şirket IÇINDE barındırılan IR seçin. Ya da henüz yoksa bir tane oluşturun.

      Şirket içinde barındırılan yeni bir IR oluşturmak için **+ Yeni**' yi seçin ve ardından **Şirket içinde barındırılan**' ı seçin. Bir **ad**girin ve ardından **İleri**' yi seçin. Geçerli bilgisayara yüklemek için **Hızlı Kurulum** ' u seçin veya sunulan **el ile kurulum** adımlarını izleyin.

      [Önkoşullar](#prerequisites)bölümünde belirtildiği gibi, şirket IÇINDE barındırılan IR 'nin çalıştığı bilgisayarda 3,0 MICROSOFT .net Için SAP bağlayıcısının yüklü olduğundan emin olun.

   2. SAP BW **sunucu adını**, **sistem numarasını**, **Istemci kimliğini,** **dili** ( **en**dışında), **Kullanıcı adını**ve **parolayı**girin.

   3. Ayarları doğrulamak için **Bağlantıyı Sına** ' yı seçin ve ardından **son**' u seçin.

   4. Yeni bir bağlantı oluşturulur. **İleri**’yi seçin.

5. **Açık hub hedeflerini seçin** sayfasında, SAP BW kullanılabilen açık hub hedeflerine gözatamazsınız. Verileri kopyalamak için OHD 'yi seçin ve ardından **İleri**' yi seçin.

   ![SAP BW açık hub hedef tablosu seçin](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Gerekiyorsa bir filtre belirtin. OHD 'niz yalnızca tek bir istek KIMLIĞIYLE tek bir veri aktarım işlemi (DTP) yürütmeden veri içeriyorsa veya DTP 'nizin tamamlantığınızdan ve verileri kopyalamak istediğinizden emin değilseniz, **son Isteği hariç tut** onay kutusunu temizleyin.

   Bu ayarlar hakkında daha fazla bilgi edinmek için bu makaledeki [SAP BW hub hedef yapılandırması](#sap-bw-open-hub-destination-configurations) ' nı açın. Hangi verilerin döndürüleceğini çift denetlemek için **Doğrula** ' yı seçin. Sonra **İleri**’yi seçin.

   ![SAP BW açık hub filtresini yapılandırma](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. **Hedef veri deposu** sayfasında **+ yeni bağlantı oluştur** > ' ı seçin **Azure Data Lake Storage 2.** **devam** > .

8. **Azure Data Lake Storage bağlantı belirle** sayfasında, bağlantı oluşturmak için aşağıdaki adımları izleyin.

   ![ADLS 2. bağlı hizmet sayfası oluşturma](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. **Ad** açılır listesinden Data Lake Storage 2. özellikli hesabınızı seçin.
   2. Bağlantıyı oluşturmak için **son** ' u seçin. Sonra **İleri**’yi seçin.

9. **Çıktı dosyasını veya klasörünü seçin** sayfasında, çıkış klasörü adı olarak **copyfromopenhub** yazın. Sonra **İleri**’yi seçin.

   ![Çıkış klasörü seçin sayfası](media/load-sap-bw-data/choose-output-folder.png)

10. **Dosya biçimi ayarı** sayfasında, varsayılan ayarları kullanmak için **İleri** ' yi seçin.

    ![Havuz biçim sayfasını belirtin](media/load-sap-bw-data/specify-sink-format.png)

11. **Ayarlar** sayfasında, **performans ayarları**' nı genişletin. Paralel olarak SAP BW yükleme için 5 gibi **kopya paralellik derecesi** için bir değer girin. Sonra **İleri**’yi seçin.

    ![Kopyalama ayarlarını yapılandırma](media/load-sap-bw-data/configure-copy-settings.png)

12. **Özet** sayfasında, ayarları gözden geçirin. Sonra **İleri**’yi seçin.

13. **Dağıtım** sayfasında, işlem hattını Izlemek için **izleyici** ' yi seçin.

    ![Dağıtım sayfası](media/load-sap-bw-data/deployment.png)

14. Sayfanın sol tarafındaki **izleyici** sekmesinin otomatik olarak seçili olduğuna dikkat edin. Eylemler sütunu, etkinlik çalıştırma ayrıntılarını görüntüleme ve **işlem** hattını yeniden çalıştırma bağlantılarını içerir.

    ![Ardışık düzen izleme görünümü](media/load-sap-bw-data/pipeline-monitoring.png)

15. Işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için **Eylemler** sütunundaki **etkinlik çalıştırmalarını görüntüle** ' yi seçin. İşlem hattında yalnızca bir etkinlik (kopyalama etkinliği) olduğundan tek bir girdi görürsünüz. İşlem hattı-çalıştırmalar görünümüne geri dönmek için üstteki işlem **hatları** bağlantısını seçin. Listeyi yenilemek için **Yenile**’yi seçin.

    ![Etkinlik-izleme ekranı](media/load-sap-bw-data/activity-monitoring.png)

16. Her kopyalama etkinliğinin yürütme ayrıntılarını izlemek için, etkinlik izleme görünümündeki **Eylemler** ' in altında bir gözlük simgesi olan **Ayrıntılar** bağlantısını seçin. Kullanılabilir ayrıntılar, kaynaktan havuza, veri işleme, yürütme adımları ve süre ve kullanılan yapılandırmalara kopyalanmış veri hacmini içerir.

    ![Etkinlik izleme ayrıntıları](media/load-sap-bw-data/activity-monitoring-details.png)

17. **En yüksek Istek kimliğini**görüntülemek için, etkinlik izleme görünümüne geri dönün ve **Eylemler**bölümünden **Çıkış** ' ı seçin.

    ![Etkinlik çıkış ekranı](media/load-sap-bw-data/activity-output.png)

    ![Etkinlik çıkışı Ayrıntıları görünümü](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>SAP BW açık hub 'dan artımlı kopyalama

> [!TIP]
> Data Factory ' de SAP BW açık hub bağlayıcısını SAP BW nasıl kopyalar SAP BW için bkz. [Açık hub Bağlayıcısı Delta ayıklama akışı](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) . Bu makale, temel bağlayıcı yapılandırmasını anlamanıza da yardımcı olabilir.

Şimdi, SAP BW açık hub 'dan artımlı kopya yapılandırmaya devam edelim.

Artımlı kopya, **Istek kimliğini**temel alan bir "yüksek filigran" mekanizması kullanır. Bu KIMLIK, DTP tarafından açık hub hedefi SAP BW otomatik olarak oluşturulur. Aşağıdaki diyagramda bu iş akışı gösterilmektedir:

![Artımlı kopya iş akışı akış grafiği](media/load-sap-bw-data/incremental-copy-workflow.png)

Veri Fabrikası **Başlarken** sayfasında, yerleşik şablonu kullanmak için şablondan Işlem **hattı oluştur** ' u seçin.

1. **SAP BW Azure Data Lake Storage 2. şablonuna kadar artımlı kopyayı** bulmak ve seçmek için **SAP BW** arayın. Bu şablon, verileri Azure Data Lake Storage 2. kopyalar. Diğer havuz türlerine kopyalamak için benzer bir iş akışı kullanabilirsiniz.

2. Şablonun ana sayfasında, aşağıdaki üç bağlantıyı seçin veya oluşturun ve ardından pencerenin sağ alt köşesinde **Bu şablonu kullan** ' ı seçin.

   - **Azure Blob depolama**: Bu kılavuzda, *en fazla kopyalanmış istek kimliği*olan üst sınırı depolamak için Azure Blob depolamayı kullanırız.
   - **Açık Hub SAP BW**: Bu, verilerin kopyalanacağı kaynaktır. Ayrıntılı yapılandırma için önceki tam kopyalama talimatından bakın.
   - **Azure Data Lake Storage 2.** : Bu, verilerin kopyalanacağı havuzdır. Ayrıntılı yapılandırma için önceki tam kopyalama talimatından bakın.

   ![SAP BW şablondan artımlı kopyalama](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Bu şablon, aşağıdaki üç etkinliğe sahip bir işlem hattı oluşturur ve bunları başarılı olarak zincirleme hale getirir: *arama*, *veri kopyalama*ve *Web*.

   İşlem hattı **parametreleri** sekmesine gidin. Sağlamanız gereken tüm konfigürasyonları görürsünüz.

   ![SAP BW yapılandırmasından artımlı kopya](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **Sapopenhubdestinationname**: verileri kopyalamak Için Açık hub tablosu adını belirtin.

   - **ADLSGen2SinkPath**: verileri kopyalamak için hedef Azure Data Lake Storage 2. yolunu belirtin. Yol yoksa Data Factory kopyalama etkinliği yürütme sırasında bir yol oluşturur.

   - **Highsulu Markblobpath**: `container/path`gibi üst eşik değerini depolamak için yolu belirtin.

   - **Highsulu markblobname**: `requestIdCache.txt`gibi yüksek eşik değerini depolamak için blob adını belirtin. BLOB depolama alanında, The Highsulu Markblobpath + Highsulu Markblobname adlı, *Container/Path/requestIdCache. txt*gibi karşılık gelen yola gidin. 0 içerikli bir blob oluşturun.

      ![Blob içeriği](media/load-sap-bw-data/blob.png)

   - **Logicappurl**: Bu şablonda, blob depolamada yüksek filigrandaki değeri ayarlamak için Azure Logic Apps çağırmak üzere webactivity kullanırız. Ya da, Azure SQL veritabanı 'nı depolamak için kullanabilirsiniz. Değeri güncelleştirmek için saklı yordam etkinliğini kullanın.

      Aşağıdaki görüntüde gösterildiği gibi öncelikle bir mantıksal uygulama oluşturmanız gerekir. Ardından, **http post URL 'sini**yapıştırın.

      ![Mantıksal uygulama yapılandırması](media/load-sap-bw-data/logic-app-config.png)

      1. Azure portalına gidin. Yeni bir **Logic Apps** hizmeti seçin. **Logic Apps tasarımcısına**gitmek Için **+ boş mantıksal uygulama** ' yı seçin.

      2. **BIR http isteği alındığında**bir tetikleyicisi oluşturun. HTTP istek gövdesini aşağıdaki gibi belirtin:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. **BLOB oluştur** eylemi ekleyin. **Klasör yolu** ve **BLOB adı**Için, daha önce **highsulu Markblobpath** ve **highsulu markblobname**içinde yapılandırdığınız aynı değerleri kullanın.

      4. **Kaydet**’i seçin. Daha sonra, Data Factory işlem hattında kullanılacak **http post URL 'sinin** değerini kopyalayın.

4. Data Factory işlem hattı parametrelerini sağlamadıktan sonra, yapılandırmayı doğrulamak üzere bir çalıştırma çağırmak için **hata ayıkla** > **son** ' u seçin. Ya da değişiklikleri yayımlamak için **Tümünü Yayımla** ' yı seçin ve ardından bir çalıştırmayı yürütmek için **Tetikle** ' ı seçin.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW açık hub hedefi yapılandırması

Bu bölümde, verileri kopyalamak için Data Factory SAP BW açık hub bağlayıcısını kullanmak üzere SAP BW tarafının yapılandırması tanıtılmıştır.

### <a name="configure-delta-extraction-in-sap-bw"></a>SAP BW 'da Delta ayıklamayı yapılandırma

Hem geçmiş kopyasına hem de artımlı kopyaya ya da yalnızca artımlı kopyaya ihtiyacınız varsa, SAP BW Delta ayıklamayı yapılandırın.

1. Açık hub hedefini oluşturun. Gerekli dönüştürme ve veri aktarımı işlemini otomatik olarak oluşturan OHD 'nin SAP Işlem RSA1 oluşturabilirsiniz. Aşağıdaki ayarları kullanın:

   - **ObjectType**: herhangi bir nesne türünü kullanabilirsiniz. Burada, bir örnek olarak **InfoCube** kullanıyoruz.
   - **Hedef türü**: **veritabanı tablosu**seçin.
   - **Tablonun anahtarı**: **Teknik anahtar**seçin.
   - **Ayıklama**: **verileri tut ve tabloya kayıt Ekle '** yi seçin.

   ![SAP BW OHD Delta ayıklama iletişim kutusu oluşturma](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![SAP BW OHD delta2 ayıklama iletişim kutusu oluşturma](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   DTP için paralel çalışan SAP iş işlemlerinin sayısını artırabilirsiniz:

   ![oluşturma-SAP-siyah beyaz-OHD-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. İşlem zincirlerine DTP 'yi zamanlayın.

   Bir küpe yönelik Delta DTP yalnızca gerekli satırlar sıkıştırılmamışsa işe yarar. Açık hub tablosuna DTP 'den önce siyah beyaz küp sıkıştırması 'nın çalışmadığından emin olun. Bunu yapmanın en kolay yolu, DTP 'yi mevcut işlem zincirleriyle tümleştirmeniz olur. Aşağıdaki örnekte, DTP (OHD 'ye), *Ayarla* (toplu toplama) ve *daraltma* (küp sıkıştırma) adımları arasında işlem zincirine eklenir.

   ![SAP BW işlem zinciri akış grafiği oluştur](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>SAP BW 'de tam ayıklama yapılandırma

Delta ayıklamanın yanı sıra, aynı SAP BW ınfoprovider ' ı tümüyle ayıklamasını isteyebilirsiniz. Bu genellikle tam kopya yapmak, artımlı olmamak veya [değişim ayıklamayı](#resync-delta-extraction)yeniden kullanmak istediğinizde geçerlidir.

Aynı OHD için birden fazla DTP olamaz. Bu nedenle, Delta Ayıklamadan önce ek bir OHD oluşturmanız gerekir.

![SAP BW OHD tam oluşturma](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Tam yük OHD için, Delta ayıklama için farklı seçenekler seçin:

- OHD 'de: **verileri silmek ve kayıt eklemek**için **ayıklama** seçeneğini ayarlayın. Aksi takdirde, bir bant genişliği işlem zincirinde DTP 'yi tekrarlamanız sırasında veriler birçok kez ayıklanır.

- DTP: **ayıklama modunu** **tam**olarak ayarlayın. Bu görüntüde gösterildiği gibi, OHD oluşturulduktan sonra otomatik olarak oluşturulan DTP 'nin **Delta** değerinden hemen **tam** olarak değiştirilmesi gerekir:

   !["Tam" ayıklama için yapılandırılmış SAP BW OHD iletişim kutusu oluştur](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- Bant genişliği açık hub Bağlayıcısı Data Factory: **son Isteği hariç tut**' ı kapatın. Aksi takdirde, hiçbir şey ayıklanmaz.

Genellikle tam DTP 'yi el ile çalıştırırsınız. Ya da, tam DTP için bir işlem zinciri oluşturabilirsiniz. Genellikle, var olan işlem zincirlerinden bağımsız olan ayrı bir zincirdir. Her iki durumda da, *ayıklamayı başlatmak için Data Factory copy komutunu kullanarak DTP 'nin tamamlandı olduğundan emin olun*. Aksi takdirde, yalnızca kısmi veriler kopyalanacaktır.

### <a name="run-delta-extraction-the-first-time"></a>İlk kez Delta ayıklamayı Çalıştır

İlk Delta ayıklama teknik açıdan tam bir *ayıklamadır*. Varsayılan olarak SAP BW açık hub Bağlayıcısı, verileri kopyalarken son isteği dışlar. İlk Delta ayıklanması için, sonraki bir DTP, tabloda ayrı bir istek KIMLIĞI olan Delta verileri üretene kadar Data Factory kopyalama etkinliği tarafından hiçbir veri ayıklanmaz. Bu senaryoyu önlemenin iki yolu vardır:

- İlk Delta ayıklama için **son Isteği hariç tut** seçeneğini devre dışı bırakın. İlk Delta ayıklanmasını ilk kez başlamadan önce ilk Delta DTP 'nin bitmiştir.
-  Sonraki bölümde açıklandığı gibi, Delta ayıklamayı yeniden eşitleme yordamını kullanın.

### <a name="resync-delta-extraction"></a>Delta ayıklamayı yeniden eşitleme

Aşağıdaki senaryolar SAP BW küplerindeki verileri değiştirir ancak Delta DTP tarafından değerlendirilmez:

- Seçmeli silmeyi SAP BW (herhangi bir filtre koşulunu kullanarak satır)
- SAP BW isteği silme (hatalı istekler)

SAP açık hub hedefi, Data-Mart denetimli bir veri hedefi değildir (2015 sürümünden bu yana tüm SAP BW destek paketlerinde). Bu nedenle, OHD 'deki verileri değiştirmeden bir küpten verileri silebilirsiniz. Sonra küpün verilerini Data Factory yeniden eşitlemeniz gerekir:

1. Data Factory (SAP 'de tam DTP kullanarak) tam ayıklama çalıştırın.
2. Delta DTP 'si için açık hub tablosundaki tüm satırları silin.
3. Değişim DTP 'nin durumunu **getirildi**olarak ayarlayın.

Bundan sonra, sonraki Delta DTPs ve Data Factory Delta dışlamaları beklenen şekilde çalışır.

Delta DTP 'nin durumunu **getirildi**olarak ayarlamak için, Delta DTP 'yi el ile çalıştırmak için aşağıdaki seçeneği kullanabilirsiniz:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Sonraki adımlar

SAP BW Open hub Bağlayıcısı desteği hakkında bilgi edinin:

> [!div class="nextstepaction"]
>[SAP Business Warehouse açık Merkez Bağlayıcısı](connector-sap-business-warehouse-open-hub.md)
