---
title: include dosyası
description: include dosyası
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a8dd83a9584d218d56c7350d6d634bfe4610b790
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075616"
---
Bu adımda, kaç tane Azure dosya paylaşımının gerekli olduğunu değerlendirirsiniz. Tek bir Windows Server örneği (veya kümesi), 30 adede kadar Azure dosya paylaşımını eşitleyebilir.

Birimlerinizde, kullanıcılarınız ve uygulamalarınız için SMB paylaşımları olarak yerel olarak paylaştığınız daha fazla klasör olabilir. Bu senaryoyu görmenin en kolay yolu, 1:1 ' i bir Azure dosya paylaşımıyla eşleyen şirket içi bir paylaşım hakkında bir adım adım yol kullanmaktır. Tek bir Windows Server örneği için 30 ' a kadar az sayıda numaranız varsa, 1:1 eşleştirmesi önerilir.

30 ' dan fazla paylaşımınız varsa, şirket içi paylaşım 1:1 ' i bir Azure dosya paylaşımına eşleştirmek genellikle gereksizdir. Aşağıdaki seçenekleri göz önünde bulundurun.

#### <a name="share-grouping"></a>Gruplamayı paylaşma

Örneğin, insan kaynakları (HR) departmanınızda toplam 15 paylaşım varsa, tüm IK verileri tek bir Azure dosya paylaşımında depolamayı düşünebilirsiniz. Birden çok şirket içi paylaşımın tek bir Azure dosya paylaşımında depolanması, yerel Windows Server Örneğiniz üzerinde olağan 15 SMB paylaşımlarını oluşturmanızı engellemez. Yalnızca bu 15 paylaşımların kök klasörlerini ortak bir klasör altında alt klasör olarak düzenlediğiniz anlamına gelir. Daha sonra bu ortak klasörü bir Azure dosya paylaşımıyla eşitleyebilirsiniz. Bu şekilde, bu şirket içi paylaşımlar grubu için bulutta yalnızca tek bir Azure dosya paylaşımı gereklidir.

#### <a name="volume-sync"></a>Birim eşitleme

Azure Dosya Eşitleme, bir birimin kökünün bir Azure dosya paylaşımında eşitlenmesini destekler. Birim kökünü eşitlediğiniz zaman, tüm alt klasörler ve dosyalar aynı Azure dosya paylaşımıyla gider.

Birimin kökünün eşitlenmesi her zaman en iyi yanıt değildir. Birden çok konumu eşitlerken yarar vardır. Örneğin, bu işlem, eşitleme kapsamı başına öğe sayısını tutmaya yardımcı olur. Paylaşım başına 100.000.000 öğe (dosya ve klasör) ile Azure dosya paylaşımlarını ve Azure Dosya Eşitleme test etmemiz sırasında, en iyi uygulama, sayıyı tek bir paylaşımdaki 20.000.000 veya 30.000.000 altında tutmaya çalışır. Daha az sayıda öğe içeren Azure Dosya Eşitleme ayarlamak yalnızca dosya eşitleme için faydalıdır. Daha az sayıda öğe aşağıdaki gibi senaryolar da sağlar:

* Bulut içeriğinin ilk taraması daha hızlı tamamlanabilir, bu da ad alanının Azure Dosya Eşitleme etkinleştirilmiş bir sunucuda görünmesi için beklemeyi azaltır.
* Azure dosya paylaşımının anlık görüntüsünden bulut tarafında geri yükleme daha hızlı olacaktır.
* Şirket içi bir sunucunun olağanüstü durum kurtarması önemli ölçüde hızlanır.
* Doğrudan bir Azure dosya paylaşımında (eşitleme dışında) yapılan değişiklikler algılanır ve daha hızlı eşitlenebilir.

> [!TIP]
> Kaç dosya ve klasör olduğunu bilmiyorsanız, SıKıŞTı Software GmbH 'tan TreeSize aracını inceleyin.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Dağıtım eşlemesine yönelik yapılandırılmış bir yaklaşım

Bulut depolamasını sonraki bir adımda dağıtmadan önce, şirket içi klasörler ve Azure dosya paylaşımları arasında bir eşleme oluşturulması önemlidir. Bu eşleme, kaç tane ve ne Azure Dosya Eşitleme *eşitleme grubu* kaynaklarını sağlayacağınızı bildirir. Bir eşitleme grubu, Azure dosya paylaşımından ve sunucunuzdaki klasöre sahiptir ve bir eşitleme bağlantısı kurar.

Kaç Azure dosya paylaşımının gerekli olduğunu öğrenmek için aşağıdaki limitleri ve en iyi uygulamaları gözden geçirin. Bunun yapılması, eşlemenizi iyileştirmenize yardımcı olur.

* Azure Dosya Eşitleme aracısının yüklü olduğu bir sunucu en fazla 30 Azure dosya paylaşımı ile eşitlenebilir.
* Bir Azure dosya paylaşımının bir depolama hesabı içinde dağıtılması. Bu, depolama hesabının ıOPS ve aktarım hızı gibi performans numaraları için ölçek hedefi olmasını sağlar.

  Bir standart Azure dosya paylaşımının teorik olarak bir depolama hesabının teslim edebilen en yüksek performansı ortadan kaldırabilirsiniz. Tek bir depolama hesabında birden çok paylaşım yerleştirmek, bu paylaşımlar için paylaşılan bir ıOPS ve aktarım hızı havuzu oluşturduğunuz anlamına gelir. Yalnızca bu dosya paylaşımlarına Azure Dosya Eşitleme eklemeyi planlıyorsanız, birkaç Azure dosya paylaşımını aynı depolama hesabına gruplandırmak bir sorun oluşturmaz. Dikkate alınması gereken ölçümler hakkında daha derin Öngörüler için Azure dosya paylaşımının performans hedeflerini gözden geçirin. Bu sınırlamalar, performansın açık bir şekilde sağlandığı ve her bir paylaşımda garantide olduğu Premium Depolama için uygulanmaz.

  Azure dosya paylaşımının yerel olarak kullanacağı bir uygulamayı Azure 'a kaldırma planlıyorsanız Azure dosya paylaşımınızdan daha fazla performansa sahip olabilirsiniz. Bu tür bir kullanım olasısa bile, gelecekte kendi depolama hesabında tek bir standart Azure dosya paylaşımının oluşturulması en iyisidir.
* Azure bölgesi başına abonelik başına 250 depolama hesabı sınırı vardır.

> [!TIP]
> Bu bilgiler göz önünde bulundurularak, genellikle birimlerinizde bulunan birden çok üst düzey klasörü ortak, yeni bir kök dizine gruplamak gerekli hale gelir. Daha sonra bu yeni kök dizini ve gruplandırmış olduğunuz tüm klasörleri tek bir Azure dosya paylaşımında eşitleolursunuz. Bu teknik, sunucu başına 30 Azure dosya paylaşımının eşitlenme sınırının içinde kalmanıza olanak sağlar.
>
> Ortak bir kök altındaki Bu gruplandırma, verilerinize erişimi etkilemez. ACL 'larınız olduğu gibi kalır. Artık ortak bir köke değiştirdiğiniz yerel sunucu klasörlerinde olabilecek paylaşım yollarını (SMB veya NFS paylaşımları gibi) ayarlamanız gerekir. Başka hiçbir şey yok.

> [!IMPORTANT]
> Azure Dosya Eşitleme için en önemli ölçekli vektör, eşitlenmesi gereken öğe (dosya ve klasör) sayısıdır. Daha fazla ayrıntı için [Azure dosya eşitleme ölçek hedeflerini](../articles/storage/files/storage-files-scale-targets.md#azure-file-sync-scale-targets) gözden geçirin.

Her eşitleme kapsamı için öğe sayısını düşük tutmak en iyi uygulamadır. Bu, klasörleri Azure dosya paylaşımlarına eşleştirmenizde dikkate alınması gereken önemli bir faktördür. Azure Dosya Eşitleme, paylaşma başına 100.000.000 öğe (dosya ve klasör) ile test edilmiştir. Ancak, tek bir paylaşımda 20.000.000 veya 30.000.000 altındaki öğe sayısını korumak genellikle en iyisidir. Bu sayıları aşacak şekilde başlatırsanız, ad alanınızı birden çok paylaşımlara ayırın. Bu sayıların yaklaşık olarak altında kaladıysanız, birden çok şirket içi paylaşımı aynı Azure dosya paylaşımında gruplamak için devam edebilirsiniz. Bu uygulama, size büyümek üzere yer sağlar.

Sizin durumunuzda, bir klasör kümesi aynı Azure dosya paylaşımıyla mantıksal olarak eşitlenebilir (daha önce bahsedilen yeni, ortak kök klasör yaklaşımını kullanarak). Ancak, klasörleri bir Azure dosya paylaşımının yerine iki ile eşitlenecek şekilde yeniden gruplandırmak daha iyi olabilir. Sunucu genelinde dosya paylaşımının dengeli dosya ve klasör sayısını korumak için bu yaklaşımı kullanabilirsiniz. Ayrıca, şirket içi paylaşımlarınızı bölebilir ve daha fazla şirket içi sunucu arasında eşitleme yapabilirsiniz ve ek sunucu başına 30 daha fazla Azure dosya paylaşımı ile eşitleme özelliği ekleyebilirsiniz.

#### <a name="create-a-mapping-table"></a>Eşleme tablosu oluşturma

:::row:::
    :::column:::
        [![Eşleme tablosuna bir örnek. Bu görüntünün içeriğini denemek ve kullanmak için aşağıdaki dosyayı indirin.](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Kaç Azure dosya paylaşımının gerekli olduğunu ve mevcut verilerinizin hangi bölümlerinin Azure dosya paylaşımında biteceğini belirlemede yardımcı olması için önceki kavramların birleşimini kullanın.
        
        Gerektiğinde bu kayda başvurabilmeniz için düşüncelerinizi kaydeden bir tablo oluşturun. Aynı anda birçok Azure kaynağını sağladığınızda, eşleme planınızın ayrıntılarını kaybetmek kolay olabileceğinden, organize edilmesi önemlidir. Kapsamlı bir eşleme oluşturmanıza yardımcı olması için bir Microsoft Excel dosyasını şablon olarak indirebilirsiniz.

[//]: # (HTML, çalışma görüntüsü ayrıştırma ve metin/köprü ile aynı satırda iç içe geçmiş iki sütunlu bir tablo ekleme gerçekleştirmenin tek yolu olarak görünür.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Bir ad alanı eşleme şablonu indirin.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
