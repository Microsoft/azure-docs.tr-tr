---
title: Azure uzamsal bağlantıları hakkında sık sorulan sorular | Microsoft Docs
description: Azure uzamsal bağlantıları, HoloLens, iOS ve Android cihazlarda cihazlar arası, çok kullanıcılı, karma gerçeklik deneyimlerini sağlayan, yönetilen bir bulut hizmeti ve geliştirici platformudur. Bu SSS 'ler, hizmet hakkındaki soruları teknik bir bakış noktasından ele adreslardır.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 06936a196813dacfc1fc9d02945bee4119b7eea8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927505"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Azure Spatial Anchors hakkında sık sorulan sorular

Azure uzamsal bağlantıları, HoloLens, iOS ve Android cihazlarda çok kullanıcılı, istenmeyen, dağınık karma gerçeklik deneyimlerini sağlayan, yönetilen bir bulut hizmeti ve geliştirici platformudur.

Daha fazla bilgi için bkz. [Azure uzamsal Tutturucuların genel bakış](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Azure uzamsal çıpası ürün SSS

**S: Azure uzamsal bağlantıları hangi cihazları destekler?**

**C:** Azure uzamsal bağlantıları, geliştiricilerin ARKit desteği olan iOS cihazlarında ve ARCore desteğiyle Android cihazlarda uygulama oluşturmalarına olanak sağlar; iOS ve Android için bu, hem telefonları hem de tabletleri içerir.

**S: Azure uzamsal bağlayıcılarını kullanmak için buluta bağlı olmam gerekir mi?**

**C:** Azure uzamsal Tutturucuların Şu anda internet 'e bir ağ bağlantısı olması gerekir. [Geri bildirim sitemiz](https://feedback.azure.com/forums/919252-azure-spatial-anchors)hakkındaki yorumlarınızı inceledik.

**S: Azure uzamsal Tutturucuların bağlantı gereksinimleri nelerdir?**

**C:** Azure uzamsal bağlantıları Wi-Fi ve mobil geniş bant bağlantılarıyla birlikte kullanılabilir.

**S: Azure uzamsal bağlantıları bağlantıları ne kadar doğru bulabilir?**

**C:** Birçok etken, bağlama koşullarını, ortamdaki nesneleri, hatta bağlayıcının yerleştirildiği yüzeyi bulmaya yönelik doğruluğu etkiler. Doğruluğun gereksinimlerinizi karşılayıp karşılamadığını öğrenmek için, bu bağlantıları kullanmayı planladığınız ortamlar temsilcisinden deneyin. Doğruluğunun gereksinimlerinizi karşılamadığı ortamlarda karşılaşırsanız bkz. [Azure uzamsal bağlayıcılarda günlüğe kaydetme ve tanılama](./concepts/logging-diagnostics.md).

**S: Bağlayıcıları oluşturma ve bulma ne kadar sürer?**

**C:** Bağlantı oluşturma ve bulma için gereken süre birçok etkene bağlıdır--ağ bağlantısı, cihazın işleme ve yükleme ve belirli ortam. BT, perakende ve oyun gibi birçok sektörde, hizmetin senaryoları için harika bir kullanıcı deneyimine izin vermediğini belirten uygulamalar oluşturuyor.

## <a name="privacy-faq"></a>Gizlilik SSS

**S: Uygulamamın bir uzamsal bağlayıcı yerleştirme durumunda tüm uygulamaların bu konuma erişimi var mı?**

**C:** Çıpaları Azure hesabı tarafından yalıtılmıştır. Yalnızca hesabınıza erişim izni verdiğiniz uygulamalar, hesap içindeki Tutturuculara erişebilecektir.

**S: Azure uzamsal bağlayıcıları kullanılırken bir ortamla ilgili hangi bilgiler iletilir ve hizmete depolanır? Aktarılan ve depolanan ortamın resimleri mı var?**

Y: Bağlantıları oluştururken veya bulurken, ortamın resimleri, cihazda türetilmiş bir biçimde işlenir. Bu türetilmiş biçim öğesine iletilir ve hizmete depolanır.

Saydamlığın sağlanması için bir ortamın görüntüsü ve türetilmiş seyrek nokta bulutu aşağıda verilmiştir. Nokta bulutu, hizmet üzerinde aktarılan ve depolanan ortamın geometrik temsilini gösterir. Seyrek nokta bulutundaki her bir nokta için, bu noktanın görsel özelliklerinin karmasını ilettik ve depolarız. Karma, herhangi bir piksel verisini içermez, ancak içermez.

Azure uzamsal bağlantıları, [Azure hizmet sözleşmesi koşullarına](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9)ve [Microsoft gizlilik bildirimine](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409)uyar.

![Ortam ve türetilmiş seyrek nokta bulutu](./media/sparse-point-cloud.png)
*Şekil 1: Bir ortam ve onun türetilmiş seyrek nokta bulutu*


**S: Microsoft 'a tanılama bilgilerini gönderemem için bir yol var mı?**

Y: Evet. Azure uzamsal bağlayıcılarının, geliştiricilerin Azure uzamsal bağlayıcı API 'SI aracılığıyla kabul etmek için seçim yapabileceğiniz bir tanılama modu vardır. Bu, örneğin, oluşturma ve bağlayıcıları tahmin edilemeyecek şekilde tespit edilebileceği bir ortamla karşılaşırsanız yararlı olur. Hata ayıklamamıza yardımcı olan bilgiler içeren bir tanılama raporu gönderebildiğinize sorabilirsiniz. Daha fazla bilgi için bkz. [Azure uzamsal bağlayıcılarda günlüğe kaydetme ve tanılama](./concepts/logging-diagnostics.md).

## <a name="availability-and-pricing-faqs"></a>Kullanılabilirlik ve fiyatlandırma SSS

**S: SLA sağlıyor musunuz?**

**C:** Azure hizmetleri için standart olduğu gibi,% 99,9 ' den büyük bir kullanılabilirliği hedefliyoruz. Azure uzamsal Çıpaların Şu anda önizleme aşamasında olduğunu ve [Önizleme ek koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'nın geçerli olduğunu unutmayın.

**S: Uygulamalarımı Azure uzamsal bağlayıcılarını uygulama mağazalarına kullanarak yayımlayabilirim miyim? Görev açısından kritik üretim senaryolarında Azure uzamsal bağlayıcılarını kullanabilir miyim?**

**C:** Azure uzamsal çıpası Şu anda önizleme aşamasındadır ve bu süre boyunca uygulama geliştirmeye, ürünle ilgili [geri bildirim sağlamanıza](https://feedback.azure.com/forums/919252-azure-spatial-anchors) ve üretim dağıtımlarınızı planlamaya davet ediyoruz.

Genel kullanılabilirlik (GA) tarihleri yakında duyurulacak.

**S: Herhangi bir azaltma sınırlarınız var mı?**

Y: Evet, azaltma sınırımız var.  Bunlara, tipik uygulama geliştirme ve test için ulaşacağız. Üretim dağıtımları için, müşterilerimizin yüksek ölçekli gereksinimlerini desteklemeye hazırız. Tartışmak için [bizimle Iletişim kurun](mailto:azuremrs@microsoft.com) . Bu önizleme aşamasında, katmanlama ve fiyatlandırma yapınızı henüz yayımladık, ancak bu işlemi yakında yapacağız.

**S: Azure uzamsal çıpası hangi bölgelerde kullanılabilir?**

**C:** Azure Doğu ABD 2 bölgesinde bugün bir Azure uzamsal bağlayıcı hesabı oluşturabilirsiniz. Bunun anlamı, bu hizmeti destekleyen işlem ve depolama alanının bu bölgede olduğunu gösterir. Bu şekilde, istemcilerinizin bulunduğu yerde bir kısıtlama yoktur. Gelecekte, hizmetin bölgesel kullanılabilirliğini tüm birincil Azure bölgelerine genişleteceğiz.

**S: Azure Spatial Anchors için ücret alıyor musunuz? Ücret ödemeniz gerekecek mi?**

**C:** Fiyatlandırma sayfamız sırasında, önizleme sırasında fiyatlandırma hakkındaki ayrıntıları [](https://azure.microsoft.com/pricing/details/spatial-anchors/)bulabilirsiniz.

## <a name="technical-faqs"></a>Teknik SSS

**S: Azure uzamsal çıpası nasıl çalışır?**

**C:** Azure uzamsal bağlantıları, karma gerçeklik/genişletilmiş gerçeklik izleyicileri 'ne bağlıdır. Bu izleyiciler, ortamı kameralardan çevrelenir ve alana hareket ederken cihazı 6-serbestlik (6DoF) olarak izler.

Yapı bloğu olarak bir 6DoF izleyici verildiğinde, Azure uzamsal bağlantıları gerçek ortamınızda "bağlayıcı" noktaları olarak belirli noktaları tanımlamanızı sağlar. Örneğin, gerçek dünyada belirli bir yerde içerik işlemek için bir tutturucu kullanabilirsiniz.

Bir tutturucu oluşturduğunuzda, istemci SDK 'Sı bu noktanın etrafında ortam bilgilerini yakalar ve hizmete iletir. Diğer bir cihaz aynı alanda yer alan bir bağlantı için görünüyorsa, benzer veriler hizmete iletir. Bu veriler, daha önce depolanan ortam verileriyle eşleştirilir. Cihaza göre bağlayıcının konumu daha sonra uygulamada kullanılmak üzere geri gönderilir.

**S: Azure uzamsal bağlantıları iOS ve Android 'de ARKit ve ARCore ile nasıl tümleştirilir?**

**C:** Azure uzamsal bağlantıları, ARKit ve ARCore 'un yerel izleme yeteneklerini kullanır. Ayrıca, iOS ve Android için SDK 'larımız, yönetilen bir bulut hizmetindeki kalıcı Çıpaları gibi yetenekler sunar ve uygulamalarınızın yalnızca hizmete bağlanarak bu bağlantıları tekrar bulmasını sağlar.

**S: Azure uzamsal bağlantıları HoloLens ile nasıl tümleştirilir?**

**C:** Azure uzamsal bağlantıları, HoloLens 'in yerel izleme yeteneklerini kullanır. HoloLens 'te uygulama oluşturmaya yönelik bir Azure uzamsal bağlayıcı SDK 'Sı sunuyoruz. SDK, yerel HoloLens özellikleri ile tümleşir ve ek yetenekler sağlar. Bu yetenekler, uygulama geliştiricilerinin bağlantıları yönetilen bir bulut hizmetinde kalıcı hale getirebilmesine ve uygulamalarınızın hizmete bağlanarak bu bağlantıları yeniden bulmasını sağlar.

**S: Azure uzamsal bağlantıları hangi platformları ve dilleri destekler?**

**C:** Geliştiriciler, cihazları için tanıdık araçları ve çerçeveleri kullanarak Azure uzamsal bağlayıcılarıyla uygulama oluşturabilir:

- HoloLens, iOS ve Android genelinde Unity
- İOS üzerinde Swift veya amaç-C
- Android üzerinde Java veya Android NDK
- C++HoloLens üzerinde/Wınrt

[Geliştirmeye buradan](index.yml)başlayın.

**S: Gerçek zamanlı değil mi çalışıyor?**

**C:** Unreal desteği gelecekte kabul edilecek.

**S: Azure uzamsal bağlantıları hangi bağlantı noktaları ve protokoller kullanır?**

**C:** Azure uzamsal bağlantıları, TCP bağlantı noktası 443 üzerinden şifrelenmiş bir protokol kullanarak iletişim kurar. Kimlik doğrulaması için, 443 bağlantı noktası üzerinden HTTPS kullanarak iletişim kuran [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)kullanır.
