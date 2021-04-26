---
title: Azure Data Lake Storage 1. 'de şifreleme | Microsoft Docs
description: Azure Data Lake Storage 1. şifreleme, verilerinizi korumanıza, kurumsal güvenlik ilkeleri uygulamanıza ve yasal uyumluluk gereksinimlerini karşılamanıza yardımcı olur. Bu makale tasarıma genel bir bakış sunarken uygulamanın birkaç teknik yönünü ele almaktadır.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: 51c9f924c6fe3ac6db86d60e26749d35bc4ab733
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108953"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage 1. verilerin şifrelenmesi

Azure Data Lake Storage 1. şifreleme, verilerinizi korumanıza, kurumsal güvenlik ilkeleri uygulamanıza ve yasal uyumluluk gereksinimlerini karşılamanıza yardımcı olur. Bu makale tasarıma genel bir bakış sunarken uygulamanın birkaç teknik yönünü ele almaktadır.

Data Lake Storage 1., hem bekleyen hem de aktarım sırasında verilerin şifrelenmesini destekler. Bekleyen veriler için, Data Lake Storage 1. "varsayılan olarak," saydam şifrelemeyi destekler. Bu terimlerin biraz daha ayrıntılı olarak anlamı şudur:

* **Varsayılan olarak açık**: yeni bir Data Lake Storage 1. hesabı oluşturduğunuzda, varsayılan ayar şifrelemeyi etkinleştirilir. Bundan sonra, Data Lake Storage 1. depolanan veriler kalıcı medyada depolanmadan önce her zaman şifrelenir. Bu durum tüm veriler için geçerlidir ve bir hesap oluşturulduktan sonra değiştirilemez.
* **Saydam**: Data Lake Storage 1. kalıcı yapmadan önce verileri otomatik olarak şifreler ve almadan önce verilerin şifresini çözer. Şifreleme Data Lake Storage 1. hesap düzeyinde bir yönetici tarafından yapılandırılır ve yönetilir. Veri erişimi API'lerinde hiçbir değişiklik yapılmaz. Bu nedenle, şifreleme nedeniyle Data Lake Storage 1. etkileşimde bulunan uygulamalarda ve hizmetlerde hiçbir değişiklik yapılması gerekmez.

Yoldaki veriler (Ayrıca, hareket halinde veri olarak da bilinir) Data Lake Storage 1. içinde her zaman şifrelenir. Kalıcı medyaya depolama önce veri şifrelemeye ek olarak, aktarımdaki veriler de her zaman HTTPS kullanılarak korunmaktadır. HTTPS, Data Lake Storage 1. REST arabirimleri için desteklenen tek protokoldür. Aşağıdaki diyagramda verilerin Data Lake Storage 1. nasıl şifrelendiği gösterilmektedir:

![Data Lake Storage 1. veri şifreleme diyagramı](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Data Lake Storage 1. ile şifrelemeyi ayarlama

Data Lake Storage 1. için şifreleme, hesap oluşturma sırasında ayarlanır ve varsayılan olarak her zaman etkindir. Anahtarları kendiniz yönetebilir veya Data Lake Storage 1. sizin için yönetmesine izin verebilirsiniz (bu varsayılandır).

Daha fazla bilgi için bkz [. Başlarken](./data-lake-store-get-started-portal.md).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Şifreleme Data Lake Storage 1. nasıl kullanılır

Aşağıdaki bilgiler, ana şifreleme anahtarlarının nasıl yönetileceğini anlatmaktadır ve Data Lake Storage 1. için veri şifrelemede kullanabileceğiniz üç farklı anahtar türünü açıklar.

### <a name="master-encryption-keys"></a>Ana şifreleme anahtarları

Data Lake Storage 1., ana şifreleme anahtarlarının (MEKs) yönetimi için iki mod sağlar. Şimdilik, ana şifreleme anahtarının en üst düzey anahtar olduğunu varsayabiliriz. Data Lake Storage 1. ' de depolanan verilerin şifresini çözmek için ana şifreleme anahtarına erişim gerekir.

Ana şifreleme anahtarını yönetmek için kullanılan iki mod şunlardır:

*   Hizmet tarafından yönetilen anahtarlar
*   Müşteri tarafından yönetilen anahtarlar

Her iki modda da ana şifreleme anahtarı Azure Key Vault’ta depolanarak güvenlik altına alınır. Azure üzerinde tam olarak yönetilen, yüksek güvenlikli bir hizmet olan Key Vault, şifreleme anahtarlarını korumak için kullanılabilir. Daha fazla bilgi için bkz. [Key Vault](https://azure.microsoft.com/services/key-vault).

MEK’leri yönetmek için kullanılan iki modun sağladığı özelliklerin kısa bir karşılaştırması aşağıdadır.

| Soru | Hizmet tarafından yönetilen anahtarlar | Müşteri tarafından yönetilen anahtarlar |
| -------- | -------------------- | --------------------- |
|Veriler nasıl depolanır?|Depolanmadan önce her zaman şifrelenir.|Depolanmadan önce her zaman şifrelenir.|
|Ana Şifreleme Anahtarı nerede depolanır?|Key Vault|Key Vault|
|Key Vault dışında açıkta saklanan şifreleme anahtarı var mı? |Hayır|Hayır|
|Key Vault’tan MEK alınabilir mi?|Hayır. MEK Key Vault’ta depolandıktan sonra yalnızca şifreleme ve şifre çözme amacıyla kullanılabilir.|Hayır. MEK Key Vault’ta depolandıktan sonra yalnızca şifreleme ve şifre çözme amacıyla kullanılabilir.|
|Key Vault örneği ve MEK kime aittir?|Data Lake Storage 1. hizmeti|Kendi Azure aboneliğiniz kapsamında bulunan Key Vault örneği size aittir. Key Vault’taki MEK, yazılım veya donanım tarafından yönetilebilir.|
|Data Lake Storage 1. hizmeti için MEK erişimini iptal edebilir misiniz?|Hayır|Evet. Key Vault erişim denetim listelerini yönetebilir ve Data Lake Storage 1. hizmeti için hizmet kimliğine erişim denetim girdilerini kaldırabilirsiniz.|
|MEK’i kalıcı olarak silebilir misiniz?|Hayır|Evet. MEK ' dan Key Vault silerseniz, Data Lake Storage 1. hesabındaki verilerin Data Lake Storage 1. hizmeti de dahil herkes tarafından şifresi çözülemez. <br><br> MEK’i Key Vault'tan silmeden önce özellikle yedeklediyseniz, MEK geri yüklenebilir ve veriler kurtarılabilir. Ancak, Key Vault silinmeden önce MEK 'yi yedeklediniz, bundan sonra Data Lake Storage 1. hesabındaki verilerin şifresi çözülemez.|


MEK ve MEK’in içinde bulunduğu Key Vault’un kim tarafından yönetildiği konusundaki bu farklılık dışında, tasarımın geri kalanı her iki mod için aynıdır.

Ana şifreleme anahtarları için modu seçtiğinizde aşağıdakileri unutmamanız gerekir:

*   Data Lake Storage 1. bir hesap sağladığınızda, müşteri tarafından yönetilen anahtarların mi yoksa hizmet tarafından yönetilen anahtarların mi kullanılacağını seçebilirsiniz.
*   Data Lake Storage 1. hesap sağlandıktan sonra mod değiştirilemez.

### <a name="encryption-and-decryption-of-data"></a>Verilerin şifrelenmesi ve şifresinin çözülmesi

Veri şifreleme tasarımında kullanılan üç tür anahtar vardır. Aşağıdaki tabloda bir özet verilmektedir:

| Anahtar                   | Kısaltma | İlişkili olduğu yer: | Depolama konumu                             | Tür       | Notlar                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Ana Şifreleme Anahtarı | MEK          | Data Lake Storage 1. hesabı | Key Vault                              | Asimetrik | Data Lake Storage 1. veya sizin için yönetilebilir.                                                              |
| Veri Şifreleme Anahtarı   | DEK          | Data Lake Storage 1. hesabı | Kalıcı depolama, Data Lake Storage 1. hizmeti tarafından yönetiliyor | Simetrik  | DEK, MEK ile şifrelenir. Şifrelenmiş DEK, kalıcı medyada depolanır. |
| Blok Şifreleme Anahtarı  | BEK          | Bir veri bloğu | Yok                                         | Simetrik  | BEK, DEK’ten ve veri bloğundan türetilir.                                                      |

Aşağıdaki diyagram bu kavramları göstermektedir:

![Veri şifrelemesindeki anahtarlar](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Bir dosyanın şifresinin çözülmesi için kullanılan genel algoritma:
1.  Data Lake Storage 1. hesabı için DEK ' ın önbelleğe alınıp alınmayacağını denetleyin.
    - Bu koşulları karşılamıyorsa, kalıcı depolamadan şifrelenmiş DEK’i okuyun ve şifresinin çözülmesi için Key Vault’a gönderin. Şifresi çözülmüş DEK’i bellekte önbelleğe alın. Artık kullanıma hazırdır.
2.  Dosyadaki her veri bloğu için:
    - Kalıcı depolama alanından şifrelenmiş veri bloğu okunur.
    - DEK’ten ve şifrelenmiş veri bloğundan BEK oluşturulur.
    - Verilerin şifresini çözmek için BEK kullanılır.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Bir veri bloğu şifrelenecek olduğunda genel algoritma:
1.  Data Lake Storage 1. hesabı için DEK ' ın önbelleğe alınıp alınmayacağını denetleyin.
    - Bu koşulları karşılamıyorsa, kalıcı depolamadan şifrelenmiş DEK’i okuyun ve şifresinin çözülmesi için Key Vault’a gönderin. Şifresi çözülmüş DEK’i bellekte önbelleğe alın. Artık kullanıma hazırdır.
2.  DEK’ten veri bloğu için benzersiz bir BEK oluşturulur.
3.  Veri bloğu, BEK ile AES-256 şifreleme kullanılarak şifrelenir.
4.  Şifrelenmiş veri bloğu kalıcı depolama alanında depolanır.

> [!NOTE] 
> DEK her zaman, kalıcı medyada veya bellekte önbelleğe alınmış olarak MEK tarafından şifrelenmiş olarak depolanır.

## <a name="key-rotation"></a>Anahtar döndürme

Müşteri tarafından yönetilen anahtarları kullanırken MEK’i döndürebilirsiniz. Müşteri tarafından yönetilen anahtarlarla Data Lake Storage 1. bir hesabı [ayarlamayı öğrenmek için bkz. Başlarken](./data-lake-store-get-started-portal.md).

### <a name="prerequisites"></a>Önkoşullar

Data Lake Storage 1. hesabını ayarlarken kendi anahtarlarınızı kullanmayı seçtiniz. Bu seçenek, hesap oluşturulduktan sonra değiştirilemez. Aşağıdaki adımlarda, müşteri tarafından yönetilen anahtarlar kullandığınız (Key Vault'tan kendi anahtarlarınızı seçtiğiniz) varsayılır.

Şifreleme için varsayılan seçenekleri kullanırsanız, verilerinizin Data Lake Storage 1. tarafından yönetilen anahtarlar kullanılarak her zaman şifrelendiğini unutmayın. Bu seçenekte, Data Lake Storage 1. tarafından yönetildiklerinden, anahtarları döndürme olanağınız yoktur.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Data Lake Storage 1. içinde MEK döndürme

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Data Lake Storage 1. hesabınızla ilişkili anahtarlarınızı depolayan Key Vault örneğine gidin. **Anahtarlar**’ı seçin.

    ![Key Vault ekran görüntüsü](./media/data-lake-store-encryption/keyvault.png)

3. Data Lake Storage 1. hesabınızla ilişkili anahtarı seçin ve bu anahtarın yeni bir sürümünü oluşturun. Data Lake Storage 1. Şu anda yalnızca bir anahtarın yeni bir sürümüne anahtar döndürmeyi desteklediğini unutmayın. Farklı bir anahtara döndürmeyi desteklemez.

   ![Yeni Sürümün vurgulandığı Anahtarlar penceresi ekran görüntüsü](./media/data-lake-store-encryption/keynewversion.png)

4. Data Lake Storage 1. hesabına gidin ve **şifreleme**' yi seçin.

   ![Şifreleme vurgulanmış şekilde Data Lake Storage 1. hesabı penceresinin ekran görüntüsü](./media/data-lake-store-encryption/select-encryption.png)

5. Yeni bir anahtar sürümünün mevcut olduğu bir ileti ile bildirilir. Anahtarı yeni sürüme güncelleştirmek için **anahtarı Döndür** ' e tıklayın.

   ![İleti ve döndürme anahtarı vurgulanmış Data Lake Storage 1. pencerenin ekran görüntüsü](./media/data-lake-store-encryption/rotatekey.png)

Bu işlem iki dakikadan kısa sürer ve anahtar döndürme nedeniyle beklenen kapalı kalma süresi yoktur. İşlem tamamlandıktan sonra anahtarın yeni sürümü kullanılır.

> [!IMPORTANT]
> Anahtar döndürme işlemi tamamlandıktan sonra, anahtarın eski sürümü artık yeni verileri şifrelemek için etkin olarak kullanılmaz. Ancak, eski verilere erişmenin eski anahtara ihtiyacı olabileceğinden durumlar olabilir. Bu gibi eski verilerin okunmasına izin vermek için eski anahtarı silmeyin