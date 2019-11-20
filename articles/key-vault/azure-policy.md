---
title: Azure Key Vault Azure Ilkesiyle tümleştirme
description: .NET istemci kitaplığı 'nı (v4) kullanarak bir Azure Anahtar Kasası 'ndan gizli dizileri oluşturmayı, almayı ve silmeyi öğrenin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/12/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: b59b367d38f95bafe7d3e1320d1365f708dc75dd
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077375"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Azure Key Vault Azure Ilkesiyle tümleştirme

[Azure ilkesi](../governance/policy/index.yml) , kullanıcılara Azure ortamlarını ölçeklendirerek denetleme ve yönetme yeteneği veren bir idare aracıdır. Azure Ilkesi, atanan ilke kurallarıyla uyumlu olduklarından emin olmak için Azure kaynaklarında guardrayları yerleştirme yeteneği sağlar. Kullanıcıların Azure ortamının denetimini, gerçek zamanlı olarak uygulanmasını ve düzeltmesini gerçekleştirmesini sağlar. İlke tarafından gerçekleştirilen denetimlerin sonuçları, uyumluluk panosundaki kullanıcılar tarafından kullanılabilir, ancak hangi kaynakların ve bileşenlerin uyumlu olduğunu ve hangilerinin uyumlu olduğunu görebilecektir.  Daha fazla bilgi için bkz. [Azure ilke hizmeti 'Ne genel bakış](../governance/policy/overview.md).

Örnek kullanım senaryoları:

- Şirketinizin anahtar kasalarındaki en düşük anahtar boyutlarına ve en yüksek geçerlilik sürelerine sahip gereksinimleri uygulayarak şirketinizin güvenlik duruşunu geliştirmek istiyorsunuz, ancak hangi takımların uyumlu olduğunu ve hangilerinin olmadığını bilemezsiniz. 
- Şu anda kuruluşunuzda bir denetim gerçekleştirmeye yönelik bir çözümünüz yok veya kuruluşunuzdaki bireysel ekiplerinin uyumluluğunu raporlamalarını isteyerek ortamınıza el ile denetim oynamış olursunuz. Bu görevi otomatik hale getirmeyi, denetimleri gerçek zamanlı olarak gerçekleştirmeyi ve denetimin doğruluğunu güvence altına almak için bir yol arıyorsunuz.
- Şirket güvenlik ilkelerinizi zorlamak ve kişilerin otomatik olarak imzalanan sertifikalar oluşturmasını durdurmak istiyorsunuz, ancak bunların oluşturulmasını engellemenin otomatik bir yolu yoktur. 
- Test ekipleriniz için bazı gereksinimleri rahat hale getirme, ancak üretim ortamınız üzerinde sıkı denetimleri sürdürmek istiyorsunuz. Kaynaklarınızın zorlanması için basit bir otomatik yola ihtiyacınız vardır. 
- Canlı site sorunu durumunda yeni ilkelerin zorlanmasını geri alabileceğinizden emin olmak istiyorsunuz. İlke zorlamayı devre dışı bırakmak için tek tıklamayla bir çözüme ihtiyacınız vardır. 
- Ortamınızı denetlemek için 3. taraf çözümüne bağlı olursunuz ve dahili bir Microsoft teklifi kullanmak istersiniz. 

## <a name="types-of-policy-effects-and-guidance"></a>İlke etkileri ve kılavuz türleri

**Denetim**: bir ilkenin etkisi denetim olarak ayarlandığında, bu ilke ortamınızda hiçbir bozucu değişikliğe neden olmaz. Bu bileşenler, ilke uyumluluk panosunda uyumsuz olarak işaretlenerek, yalnızca belirtilen kapsamdaki ilke tanımlarına uymayan sertifikalar gibi bileşenlerde sizi uyarır. İlke efekti seçilmezse, denetim varsayılan olarak ayarlanır. 

**Reddet**: bir ilkenin etkisi Reddet olarak ayarlandığında, ilke, sertifikalar gibi yeni bileşenlerin oluşturulmasını ve ilke tanımıyla uyumlu olmayan mevcut bileşenlerin yeni sürümlerini engellemeyi engeller. Bir Anahtar Kasası içindeki uyumlu olmayan mevcut kaynaklar etkilenmez. ' Denetim ' özellikleri çalışmaya devam edecektir.

## <a name="available-built-in-policy-definitions"></a>Kullanılabilir "yerleşik" Ilke tanımları

Key Vault, sertifikaları yönetmek üzere yaygın senaryolar için atayabileceğiniz bir dizi ilke oluşturdu. Bu ilkeler ' yerleşik ' ' dır, bu, bunları etkinleştirmek için özel JSON yazmanıza gerek kalmaz ve atamanız için Azure portal kullanılabilir hale gelir. Kuruluşunuzun ihtiyaçlarına uyacak şekilde belirli parametreleri de özelleştirebilirsiniz. 

Sekiz önizleme ilkesi aşağıdaki gibidir.

### <a name="manage-certificate-validity-period-preview"></a>Sertifika geçerlilik süresini yönetme (Önizleme)

Bu ilke, anahtar kasasında depolanan sertifikalarınızın maksimum geçerlilik süresini yönetmenizi sağlar. Sertifikalarınızın maksimum geçerlilik süresini sınırlamak için iyi bir güvenlik uygulamasıdır. Sertifikanıza özel bir anahtar algılanmadan tehlikeye atılırsa, kısa süreli sertifikaların kullanılması, devam eden hasar için zaman çerçevesini en aza indirir ve sertifikanın değerini bir saldırgan olarak azaltır. 

### <a name="manage-allowed-certificate-key-types-preview"></a>İzin verilen sertifika anahtar türlerini yönetme (Önizleme)
Bu ilke, Anahtar Kasanızda olabilecek sertifika türlerini kısıtlayabilmeniz için izin verir. Bu ilkeyi, sertifika özel anahtarlarınızın RSA, ECC veya HSM 'nin desteklenen olduğundan emin olmak için kullanabilirsiniz. Hangi sertifika türlerine izin verileceğini aşağıdaki listeden seçebilirsiniz.
- RSA
- RSA-HSM
- ECC 
- ECC-HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Sertifika yaşam süresi eylem tetikleyicilerini yönetme (Önizleme)

Bu ilke, belirli bir gün sayısı içinde olan veya kullanım ömrünün belirli bir yüzdesine ulaşan sertifikalar için belirtilen yaşam süresi eylemini yönetmenizi sağlar. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Tümleşik bir CA tarafından verilen sertifikaları yönetme (Önizleme)

Key Vault tümleşik bir sertifika yetkilisi (DigiCert veya GlobalSign) kullanıyorsanız ve kullanıcıların bu sağlayıcılardan birini veya birini kullanmasını istiyorsanız, seçiminizi denetlemek veya zorlamak için bu ilkeyi kullanabilirsiniz. Bu ilke, anahtar kasasında otomatik olarak imzalanan sertifika oluşturulmasını denetlemek veya reddetmek için de kullanılabilir. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Tümleşik bir CA tarafından verilen sertifikaları yönetme (Önizleme)

Dahili bir sertifika yetkilisi veya anahtar kasası ile tümleşik olmayan bir sertifika yetkilisi kullanırsanız ve kullanıcıların sağladığınız listeden bir sertifika yetkilisi kullanmasını istiyorsanız, bu ilkeyi veren adına göre izin verilen sertifika yetkililerinin bir listesini oluşturmak için kullanabilirsiniz. Bu ilke, anahtar kasasında otomatik olarak imzalanan sertifika oluşturulmasını denetlemek veya reddetmek için de kullanılabilir. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Eliptik Eğri Şifreleme sertifikaları için izin verilen eğri adlarını yönetme (Önizleme)
Eliptik Eğri şifrelemesi veya ECC sertifikaları kullanıyorsanız, aşağıdaki listeden, izin verilen bir eğri adları listesini özelleştirebilirsiniz. Varsayılan seçenek, tüm aşağıdaki eğri adlarına izin verir. 
- P-256
- P-256K
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>RSA sertifikaları için en düşük anahtar boyutunu Yönet (Önizleme)
RSA sertifikaları kullanıyorsanız, sertifikalarınızın sahip olması gereken en düşük anahtar boyutunu seçebilirsiniz. Aşağıdaki listeden bir seçenek belirleyebilirsiniz. 
- 2048 bit
- 3072 bit
- 4096 bit

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Belirtilen gün sayısı (Önizleme) içinde olan sertifikaları yönetin
Düzgün şekilde izlenmeyen bir sertifika, süresi dolmadan önce döndürülmediyse hizmetiniz kesintiye neden olabilir. Bu ilke, Anahtar Kasası 'nda depolanan sertifikalarınızın izlendiğinden emin olmak için önemlidir. Bu ilkeyi farklı süre sonu eşikleriyle birden çok kez uygulamanız önerilir. Örneğin, 180, 90, 60 ve 30 günlük eşikler. Bu ilke, kuruluşunuzdaki sertifika süre sonunu izlemek ve önceliklendirmek için kullanılabilir. 

## <a name="example-scenario"></a>Örnek Senaryo

100 sertifikalarını içeren birden fazla ekip tarafından kullanılan bir anahtar kasasını yönetirsiniz ve anahtar kasasındaki sertifikaların hiçbirinin 2 yıldan daha uzun bir süre geçerli olmadığından emin olmak istiyorsunuz.

1. [Sertifika geçerlilik süresini Yönet](#manage-certificate-validity-period-preview) ilkesini atarsanız, bir sertifikanın en fazla geçerlilik süresini 24 ay olacak şekilde belirtin ve ilkenin etkisini "Audit" olarak ayarlayın. 
1. [Uyumluluk raporu Azure Portal](#view-compliance-results)görüntülenir ve 20 sertifikanın uyumlu olduğunu ve > 2 yıl boyunca geçerli olduğunu ve kalan sertifikaların uyumlu olduğunu keşfedebilirsiniz. 
1. Bu sertifikaların sahiplerine başvurabilir ve sertifikaların 2 yıldan daha uzun süre geçerli olmadığı yeni güvenlik gereksinimini iletmezsiniz. Bazı takımlar yanıt verir ve 15 ' i, sertifikaların süresi 2 yıl veya daha az olan en fazla geçerlilik süresiyle yenilendi. Diğer takımlar yanıt vermez ve Anahtar Kasanızda hala 5 uyumlu olmayan sertifikanız vardır.
1. "Reddet" e atadığınız ilkenin etkisini değiştirirsiniz. 5 uyumlu olmayan sertifikalar iptal edilmez ve çalışmaya devam eder. Ancak, 2 yıldan büyük bir geçerlilik süresi ile yenilenemez. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Azure portal üzerinden Key Vault ilkesi etkinleştirme ve yönetme

### <a name="select-a-policy-definition"></a>Ilke tanımı seçin

1. Azure portalında oturum açın. 
1. Arama çubuğunda "Ilke" araması yapın ve **ilke**' yi seçin.

    ![Azure Key Vault nasıl çalıştığına genel bakış](./media/policy-img1.png)

1. Ilke penceresinde **tanımlar**' ı seçin.

    ![Azure Key Vault nasıl çalıştığına genel bakış](./media/policy-img2.png)

1. Kategori filtresinde **Tümünü Seç** seçimini kaldırın ve **Key Vault**seçin. 

    ![Azure Key Vault nasıl çalıştığına genel bakış](./media/policy-img3.png)

1. Artık Azure Key Vault için genel önizlemeye sunulan tüm ilkeleri görebilmeniz gerekir. Yukarıdaki ilke kılavuzu bölümünü okuduğunuzdan ve anladığınızdan emin olun ve bir kapsama atamak istediğiniz ilkeyi seçin.  

    ![Azure Key Vault nasıl çalıştığına genel bakış](./media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Kapsama Ilke atama 

1. Uygulamak istediğiniz ilkeyi seçin, bu örnekte **sertifika geçerlilik süresini Yönet** ilkesi görüntülenir. Sol üst köşedeki ata düğmesine tıklayın.

    ![Azure Key Vault nasıl çalıştığına genel bakış](./media/policy-img5.png)
  
1. İlkenin uygulanmasını istediğiniz aboneliği seçin. Kapsamı, bir abonelik içinde yalnızca tek bir kaynak grubuyla kısıtlamayı tercih edebilirsiniz. İlkeyi aboneliğin tamamına uygulamak ve bazı kaynak gruplarını dışlamak istiyorsanız, bir dışlama listesi de yapılandırabilirsiniz. İlkeyi (denetim ya da reddetme) gerçekleşmesini veya **devre dışı** bırakmak (denetim veya reddetme) devre dışı bırakmak istiyorsanız ilke zorlama seçiciyi **etkin** olarak ayarlayın. 

    ![Azure Key Vault nasıl çalıştığına genel bakış](./media/policy-img6.png)

1. İstediğiniz en uzun geçerlilik süresini belirtmek için ekranın üst kısmındaki parametreler sekmesine tıklayın. Yukarıdaki bölümlerdeki kılavuzdan sonra ilke için **Denetim** veya **reddetme** seçimini yapın. Sonra gözden geçir + Oluştur düğmesini seçin. 

    ![Azure Key Vault nasıl çalıştığına genel bakış](./media/policy-img7.png)

### <a name="view-compliance-results"></a>Uyumluluk sonuçlarını görüntüle

1. Ilke dikey penceresine dönüp Uyumluluk sekmesini seçin. uyumluluk sonuçlarını görüntülemek istediğiniz ilke atamasını tıklatın.

    ![Azure Key Vault nasıl çalıştığına genel bakış](./media/policy-img8.png)

1. Bu sayfadan, sonuçları uyumlu veya uyumsuz kasaların filtrelemesine göre filtreleyebilirsiniz. Burada, ilke atamasının kapsamında uyumlu olmayan anahtar kasalarının bir listesini görebilirsiniz. Kasadaki bileşenlerden herhangi biri (Sertifikalar) uyumlu değilse, kasa uyumlu değil olarak kabul edilir. Bireysel olarak uyumlu olmayan bileşenleri (Sertifikalar) görüntülemek için tek bir kasa seçebilirsiniz. 


    ![Azure Key Vault nasıl çalıştığına genel bakış](./media/policy-img9.png)

1. Bir kasadaki, uyumsuz olmayan bileşenlerin adını görüntüleme


    ![Azure Key Vault nasıl çalıştığına genel bakış](./media/policy-img10.png)

1. Kullanıcıların Anahtar Kasası içinde kaynak oluşturma yeteneğinin engellenip engellenmeyeceğini denetlemeniz gerekiyorsa, istek sahibine ve isteklerin zaman damgalarına sahip reddedilen sertifika işlemlerinin özetini görüntülemek için **bileşen olayları (Önizleme)** sekmesine tıklayabilirsiniz. 


    ![Azure Key Vault nasıl çalıştığına genel bakış](./media/policy-img11.png)

## <a name="feature-limitations"></a>Özellik sınırlamaları

Bir "reddetme" efektiyle ilke atanması, uyumlu olmayan kaynakların oluşturulmasını reddetmek için 30 dakika (ortalama Case) ve 1 saat (en kötü durum) gerektirebilir. Bir kasadaki mevcut bileşenlerin ilke değerlendirmesi, uyumluluk sonuçlarının Portal Kullanıcı arabiriminde görüntülenebilmesi için 1 saat (ortalama büyük/küçük harf) ve 2 saat (en kötü durum) kadar sürebilir. Uyumluluk sonuçları "başlatılmamış" olarak görünmüyorsa, bunun nedeni aşağıdakilerden biri olabilir:
- İlke değerlemesi henüz tamamlanmadı. İlk değerlendirme gecikmesi en kötü durum senaryosunda 2 saate kadar sürebilir. 
- İlke atamasının kapsamında herhangi bir anahtar kasası yok.
- İlke atamasının kapsamında sertifikalara sahip bir anahtar kasası yok. 

## <a name="next-steps"></a>Sonraki Adımlar

- [Azure ilke hizmeti](../governance/policy/overview.md) hakkında daha fazla bilgi edinin
- Bkz. bir örnek: [sanal ağ uç noktaları olmayan Key Vault kasaları](../governance/policy/samples/keyvault-no-vnet-rules.md)

