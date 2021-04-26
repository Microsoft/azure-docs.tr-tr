---
title: 'Öğretici: Azure statik Web Apps ile Mongoose kullanarak Cosmos DB verilere erişin'
description: Azure statik Web Apps API işlevinden Mongoose kullanarak Cosmos DB verilere erişmeyi öğrenin.
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: f64cc67ad6f0296ad289d858795ee783943f3daf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260083"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>Öğretici: Azure statik Web Apps ile Mongoose kullanarak Cosmos DB verilere erişin

[Mongoose](https://mongoosejs.com/) , Node.js için en popüler ODM (nesne belgesi eşleme) istemcimdir. Mongoose, veri yapısı tasarlamanızı ve doğrulamayı zorlamanıza izin vererek Mongoose API 'sini destekleyen veritabanlarıyla etkileşim kurmak için gereken tüm araçları sağlar. [Cosmos DB](../cosmos-db/mongodb-introduction.md) , gerekli Mongoose API 'lerini destekler ve Azure üzerinde arka uç sunucu seçeneği olarak kullanılabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> - Cosmos DB sunucusuz hesap oluşturma
> - Azure statik Web Apps oluşturma
> - Bağlantı dizesini depolamak için uygulama ayarlarını Güncelleştir

Azure aboneliğiniz yoksa [ücretsiz bir deneme hesabı](https://azure.microsoft.com/free/)oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- [Azure hesabı](https://azure.microsoft.com/free/)
- [GitHub hesabı](https://github.com/join)

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-cosmos-db-serverless-database"></a>Cosmos DB sunucusuz veritabanı oluşturma

[Cosmos DB sunucusuz](https://docs.microsoft.com/azure/cosmos-db/serverless) bir hesap oluşturarak başlayın. Sunucusuz bir hesap kullanarak yalnızca kullanılan kaynaklar için ödeme yaparsınız ve tam altyapı oluşturma gereksiniminden kaçının.

1. Şuraya gidin [https://portal.azure.com](https://portal.azure.com)
2. **Kaynak oluştur ' a** tıklayın
3. Arama kutusuna **Azure Cosmos DB** girin
4. **Azure Cosmos DB** tıklayın
5. **Oluştur** seçeneğine tıklayın
6. Azure Cosmos DB hesabınızı aşağıdaki bilgilerle yapılandırın
    - Abonelik: kullanmak istediğiniz aboneliği seçin
    - Kaynak: **Yeni oluştur**' a tıklayın ve adı **aswa-Mongoose** olarak ayarlayın
    - Hesap adı: benzersiz bir değer gereklidir
    - API: **MongoDB API 'si için Azure Cosmos DB**
    - Not defterleri (Önizleme): **kapalı**
    - Konum: **Batı ABD 2**
    - Kapasite modu: **sunucusuz (Önizleme)**
    - Sürüm: **3,6**
    - Kullanılabilirlik alanları:  
 :::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="Yeni Cosmos DB örneğini oluşturmayı"::: devre dışı bırak
7. **Gözden geçir + oluştur** 'a tıklayın
8. **Oluştur** seçeneğine tıklayın

Oluşturma işlemi birkaç dakika sürer. Sonraki adımlar, bağlantı dizesinin toplanması için veritabanına döndürülür.

## <a name="create-a-static-web-app"></a>Statik web uygulaması oluşturma

Bu öğretici, uygulamanızı oluşturmanıza yardımcı olması için bir GitHub şablon deposu kullanır.

1. [Başlangıç şablonuna](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate) gitme
2. **Sahibini** seçin (ana hesabınız dışında bir kuruluş kullanıyorsanız)
3. Deponuzu adlandırma **-Mongoose-öğretici**
4. **Şablondan depo oluştur** ' a tıklayın
5. [Azure Portal](https://portal.azure.com) dön
6. **Kaynak oluştur ' a** tıklayın
7. Arama kutusuna **statik Web uygulamaları** yazın
8. **Statik Web uygulaması seçin (Önizleme)**
9. **Oluştur** seçeneğine tıklayın
10. Azure statik Web uygulamanızı aşağıdaki bilgilerle yapılandırın
    - Abonelik: önceki abonelikle aynı aboneliği seçin
    - Kaynak grubu: **aswa-Mongoose** seçin
    - Ad: **aswa-Mongoose-öğretici**
    - Bölge: **Batı ABD 2**
    - **GitHub Ile oturum aç** ' a tıklayın
    - Dağıtımı etkinleştirmek için Azure statik Web Apps 'nin GitHub eylemi oluşturmasına izin vermeniz istenirse **Yetkilendir** 'e tıklayın
    - Kuruluş: hesap adınız
    - Depo: **aswa-Mongoose-öğretici**
    - Dal: **ana**
    - Derleme önayarları: **özel** ' i seçin
    - Uygulama konumu: **/Public**
    - API konumu: **API**
    - Uygulama yapıt konumu: *boş* 
     :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="Tamamlanan Azure statik Web Apps formu"::: bırakma
11. **Gözden geçir ve oluştur** 'a tıklayın
12. **Oluştur** seçeneğine tıklayın

Oluşturma işlemi birkaç dakika sürer, ancak uygulama sağlandıktan sonra **Kaynağa Git ' e** tıklayabilirsiniz.

## <a name="configure-database-connection-string"></a>Veritabanı bağlantı dizesini Yapılandır

Web uygulamasının veritabanıyla iletişim kurmasına izin vermek için, veritabanı bağlantı dizesi bir uygulama ayarı olarak depolanır. Ayar değerleri nesnesi kullanılarak Node.js erişilebilir `process.env` .

1. Azure portal sol üst köşesindeki **giriş** ' e tıklayın (veya geri git [https://portal.azure.com](https://portal.azure.com) )
2. **Kaynak grupları** ' na tıklayın
3. **Aswa-Mongoose** öğesine tıklayın
4. Veritabanı hesabınızın adına tıklayın; **Azure Cosmos DB hesap** türüne sahip olur
5. **Ayarlar** ' ın altında **bağlantı dizesi** ' ne tıklayın
6. **BIRINCIL bağlantı dizesi** altında listelenen bağlantı dizesini Kopyala
7. İçerik haritaları ' de, **aswa-Mongoose** ' ye tıklayın.
8. Web sitesi örneğine dönmek için **aswa-Mongoose-öğreticisi** ' ne tıklayın
9. **Ayarlar** altında **yapılandırma** ' ya tıklayın.
10. **Ekle** ' ye tıklayın ve aşağıdaki değerlerle yeni bir uygulama ayarı oluşturun
    - Ad: **CONNECTION_STRING**
    - Değer: daha önce kopyaladığınız bağlantı dizesini yapıştırın
11. **Tamam 'a** tıklayın
12. **Kaydet**’e tıklayın

## <a name="navigate-to-your-site"></a>Sitenize gidin

Artık statik Web uygulamasını inceleyebilirsiniz.

1. **Genel Bakış ' a** tıklayın
1. Sağ üst köşedeki URL 'ye tıklayın
    1. Şuna benzer şekilde görünür `https://calm-pond-05fcdb.azurestaticapps.net`
1. Başlığa yazıp **Görev Ekle** ' ye tıklayarak yeni bir görev oluşturun
1. Görevin görüntülendiğini onaylayın (Bu işlem biraz zaman alabilir)
1. **Onay kutusuna tıklayarak** görevi Tamam olarak işaretleyin
1. Bir veritabanının kullanılmakta olduğunu onaylamak için **sayfayı yenileyin**

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, kaynak grubunu aşağıdaki adımlarla silin:

1. [Azure Portal](https://portal.azure.com) dön
2. **Kaynak grupları** ' na tıklayın
3. **Aswa-Mongoose** öğesine tıklayın
4. **Kaynak grubunu sil** ' e tıklayın
5. Metin kutusuna **aswa-Mongoose** yazın
6. **Sil**’e tıklayın

## <a name="next-steps"></a>Sonraki adımlar

Yerel geliştirmeyi yapılandırma hakkında bilgi edinmek için sonraki makaleye ilerleyin...
> [!div class="nextstepaction"]
> [Yerel geliştirme ayarlama](./local-development.md)
 
