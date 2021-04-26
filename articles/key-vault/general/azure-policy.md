---
title: Azure Key Vault’u Azure İlkesiyle tümleştirme
description: Azure Key Vault Azure Ilkesiyle tümleştirme hakkında bilgi edinin
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: cddc7b931bf59412d4a7ec8e6b0eecfe148f3d5e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749285"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Azure Key Vault’u Azure İlkesiyle tümleştirme

[Azure ilkesi](../../governance/policy/index.yml) , kullanıcılara Azure ortamlarını ölçeklendirerek denetleme ve yönetme yeteneği veren bir idare aracıdır. Azure Ilkesi, atanan ilke kurallarıyla uyumlu olduklarından emin olmak için Azure kaynaklarında guardrayları yerleştirme yeteneği sağlar. Kullanıcıların Azure ortamının denetimini, gerçek zamanlı olarak uygulanmasını ve düzeltmesini gerçekleştirmesini sağlar. İlke tarafından gerçekleştirilen denetimlerin sonuçları, uyumluluk panosundaki kullanıcılar tarafından kullanılabilir ve bunların hangi kaynakların ve bileşenlerin uyumlu olduğunu ve hangilerinin uyumlu olduğunu görebilecekleri bir ayrıntıya gidebilecektir.  Daha fazla bilgi için bkz. [Azure ilke hizmeti 'Ne genel bakış](../../governance/policy/overview.md).

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

Key Vault, anahtar, sertifika ve gizli nesneleri yönetmek için kullanılabilen bir dizi ilke oluşturdu. Bu ilkeler ' yerleşik ' ' dır, bu, bunları etkinleştirmek için özel JSON yazmanıza gerek kalmaz ve atamanız için Azure portal kullanılabilir hale gelir. Kuruluşunuzun ihtiyaçlarına uyacak şekilde belirli parametreleri de özelleştirebilirsiniz.

# <a name="certificate-policies"></a>[Sertifika İlkeleri](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>Sertifikalar belirtilen en fazla geçerlilik süresine (Önizleme) sahip olmalıdır

Bu ilke, anahtar kasasında depolanan sertifikalarınızın maksimum geçerlilik süresini yönetmenizi sağlar. Sertifikalarınızın maksimum geçerlilik süresini sınırlamak için iyi bir güvenlik uygulamasıdır. Sertifikanıza özel bir anahtar algılanmadan tehlikeye atılırsa, kısa süreli sertifikaların kullanılması, devam eden hasar için zaman çerçevesini en aza indirir ve sertifikanın değerini bir saldırgan olarak azaltır.

### <a name="certificates-should-use-allowed-key-types-preview"></a>Sertifikaların izin verilen anahtar türlerini kullanması gerekir (Önizleme)

Bu ilke, Anahtar Kasanızda olabilecek sertifika türlerini kısıtlayabilmeniz için izin verir. Bu ilkeyi, sertifika özel anahtarlarınızın RSA, ECC veya HSM 'nin desteklenen olduğundan emin olmak için kullanabilirsiniz. Hangi sertifika türlerine izin verileceğini aşağıdaki listeden seçebilirsiniz.

- RSA
- RSA-HSM
- ECC
- ECC-HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>Sertifikalar belirtilen ömür eylemi tetikleyicilerine sahip olmalıdır (Önizleme)

Bu ilke, belirli bir gün sayısı içinde olan veya kullanım ömrünün belirli bir yüzdesine ulaşan sertifikalar için belirtilen yaşam süresi eylemini yönetmenizi sağlar.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>Sertifikalar, belirtilen tümleşik sertifika yetkilisi (Önizleme) tarafından verilmelidir

Key Vault tümleşik bir sertifika yetkilisi (DigiCert veya GlobalSign) kullanıyorsanız ve kullanıcıların bu sağlayıcılardan birini veya birini kullanmasını istiyorsanız, seçiminizi denetlemek veya zorlamak için bu ilkeyi kullanabilirsiniz. Bu ilke, anahtar kasasında otomatik olarak imzalanan sertifika oluşturulmasını denetlemek veya reddetmek için de kullanılabilir.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>Sertifikalar, belirtilen tümleşik olmayan sertifika yetkilisi (Önizleme) tarafından verilmelidir

Dahili bir sertifika yetkilisi veya anahtar kasası ile tümleşik olmayan bir sertifika yetkilisi kullanırsanız ve kullanıcıların sağladığınız listeden bir sertifika yetkilisi kullanmasını istiyorsanız, bu ilkeyi veren adına göre izin verilen sertifika yetkililerinin bir listesini oluşturmak için kullanabilirsiniz. Bu ilke, anahtar kasasında otomatik olarak imzalanan sertifika oluşturulmasını denetlemek veya reddetmek için de kullanılabilir.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>Eliptik Eğri şifrelemesi kullanan sertifikalar, eğri adlarına izin verebilir (Önizleme)

Eliptik Eğri şifrelemesi veya ECC sertifikaları kullanıyorsanız, aşağıdaki listeden, izin verilen bir eğri adları listesini özelleştirebilirsiniz. Varsayılan seçenek, tüm aşağıdaki eğri adlarına izin verir.

- P-256
- P-256K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>RSA şifrelemesini kullanan sertifikalar RSA sertifikaları için minimum anahtar boyutunu yönetme (Önizleme)

RSA sertifikaları kullanıyorsanız, sertifikalarınızın sahip olması gereken en düşük anahtar boyutunu seçebilirsiniz. Aşağıdaki listeden bir seçenek belirleyebilirsiniz.

- 2048 bit
- 3072 bit
- 4096 bit

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Belirtilen gün sayısı (Önizleme) içinde olan sertifikaları yönetin

Düzgün şekilde izlenmeyen bir sertifika, süresi dolmadan önce döndürülmediyse hizmetiniz kesintiye neden olabilir. Bu ilke, Anahtar Kasası 'nda depolanan sertifikalarınızın izlendiğinden emin olmak için önemlidir. Bu ilkeyi farklı süre sonu eşikleriyle birden çok kez uygulamanız önerilir. Örneğin, 180, 90, 60 ve 30 günlük eşikler. Bu ilke, kuruluşunuzdaki sertifika süre sonunu izlemek ve önceliklendirmek için kullanılabilir.

# <a name="key-policies"></a>[Anahtar Ilkeleri](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Anahtarlar, belirtilen gün sayısından daha uzun bir süre (Önizleme) için etkin olmamalıdır

Anahtarlarınızın belirtilen gün sayısından daha uzun süre etkin olmadığından emin olmak istiyorsanız, anahtarınızın ne kadar süreyle etkin olduğunu denetlemek için bu ilkeyi kullanabilirsiniz.

**Anahtarınız bir etkinleştirme tarihi ayarlandıysa**, bu ilke anahtarın **etkinleştirme tarihinden** geçerli tarihe kadar geçen gün sayısını hesaplar. Gün sayısı ayarladığınız eşiği aşarsa, bu anahtar ilkeyle uyumlu değil olarak işaretlenir.

**Anahtarınız bir etkinleştirme tarihi ayarlanmamışsa**, bu ilke anahtarın **Oluşturulma tarihinden** itibaren geçerli tarihe kadar geçen gün sayısını hesaplar. Gün sayısı ayarladığınız eşiği aşarsa, bu anahtar ilkeyle uyumlu değil olarak işaretlenir.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>Anahtarlar belirtilen şifreleme türü RSA veya EC (Önizleme) olmalıdır

Bu ilke, Anahtar Kasanızda olabilecek anahtarların türünü kısıtlayabilmeniz için izin verir. Anahtarlarınızın RSA, ECC veya HSM 'nin desteklenen olduğundan emin olmak için bu ilkeyi kullanabilirsiniz. Hangi sertifika türlerine izin verileceğini aşağıdaki listeden seçebilirsiniz.

- RSA
- RSA-HSM
- ECC
- ECC-HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>Eliptik Eğri şifrelemesi kullanan anahtarlar belirtilen eğri adlarına sahip olmalıdır (Önizleme)

Eliptik Eğri şifrelemesi veya ECC anahtarları kullanıyorsanız, aşağıdaki listeden, izin verilen bir eğri adları listesini özelleştirebilirsiniz. Varsayılan seçenek, tüm aşağıdaki eğri adlarına izin verir.

- P-256
- P-256K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>Anahtarların süre sonu tarihleri ayarlanmış olmalıdır (Önizleme)

Bu ilke, zaman aşımı tarihi uyumlu değil olarak ayarlanan anahtar kasalarınızda ve bayraklar anahtarlarındaki tüm anahtarları denetler. Bu ilkeyi, sona erme tarihi kümesi olmayan anahtarların oluşturulmasını engellemek için de kullanabilirsiniz.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Anahtarlar, süre sonundan önce belirtilen gün sayısından daha fazla olmalıdır (Önizleme)

Bir anahtar sona ermeden fazla yakınsa, anahtarı döndürmek için bir kuruluş gecikmesi bir kesinti oluşmasına neden olabilir. Anahtar, bir hataya tepki vermek için yeterli zaman sağlamak üzere, süresi dolmadan önce belirtilen gün sayısı ile döndürülmelidir. Bu ilke, sona erme tarihine kadar yakın olan anahtarları denetler ve bu eşiği gün cinsinden ayarlamanıza olanak sağlar. Bu ilkeyi Ayrıca, sona erme tarihine yakın yeni anahtarların oluşturulmasını engellemek için de kullanabilirsiniz.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>Anahtarlar bir donanım güvenlik modülü (Önizleme) ile desteklenmelidir

HSM, anahtarları depolayan bir donanım güvenlik modülüdür. HSM, şifreleme anahtarları için fiziksel bir koruma katmanı sağlar. Şifreleme anahtarı, bir yazılım anahtarından daha yüksek düzeyde güvenlik sağlayan fiziksel bir HSM 'den çıkamaz. Bazı kuruluşların, HSM anahtarlarının kullanımını zorunlu kılan uyumluluk gereksinimleri vardır. Bu ilkeyi, Anahtar Kasanızda HSM 'nin desteklenmeyen anahtarları denetlemek için kullanın. Bu ilkeyi Ayrıca HSM ile desteklenen yeni anahtarların oluşturulmasını engellemek için de kullanabilirsiniz. Bu ilke tüm anahtar türleri, RSA ve ECC için geçerlidir.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>RSA şifrelemesi kullanan anahtarların belirtilen en az anahtar boyutu olmalıdır (Önizleme)

RSA anahtarlarını daha küçük anahtar boyutlarıyla kullanmak güvenli bir tasarım uygulaması değildir. En az bir anahtar boyutu kullanımını zorunlu kılan denetim ve sertifika standartlarına tabi olabilirsiniz. Aşağıdaki ilke, anahtar kasanız üzerinde en düşük anahtar boyutu gereksinimini ayarlamanıza olanak sağlar. Bu minimum gereksinimi karşılamayan anahtarları denetleyebilirsiniz. Bu ilke, minimum anahtar boyutu gereksinimini karşılamayan yeni anahtarların oluşturulmasını engellemek için de kullanılabilir.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>Anahtarların belirtilen en fazla geçerlilik süresi (Önizleme) olmalıdır

Anahtar Kasanızda bir anahtarın geçerli olduğu maksimum süreyi gün cinsinden belirterek kurumsal uyumluluk gereksinimlerinizi yönetin. Ayarladığınız eşikten daha uzun olan anahtarlar, uyumlu değil olarak işaretlenir. Bu ilkeyi Ayrıca, bir sona erme tarihi belirlediğiniz en uzun geçerlilik süresinden daha uzun süre ayarlanmış yeni anahtarların oluşturulmasını engellemek için de kullanabilirsiniz.

# <a name="secret-policies"></a>[Gizli Ilkeler](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Gizli dizi sayısı, belirtilen gün sayısından daha uzun bir süre (Önizleme) için etkin olmamalıdır

Gizli anahtarlarınızın belirtilen gün sayısından daha uzun süre etkin olmadığından emin olmak istiyorsanız, bu ilkeyi kullanarak gizli ağınızın ne kadar süreyle etkin olduğunu denetleyebilirsiniz.

**Gizli dizinizdeki bir etkinleştirme tarihi ayarlandıysa**, bu ilke gizli dizi **etkinleştirme tarihinden** geçerli tarihe kadar geçen gün sayısını hesaplar. Gün sayısı ayarladığınız eşiği aşarsa, parola ilkeyle uyumlu değil olarak işaretlenir.

**Gizli dizinizdeki bir etkinleştirme tarihi ayarlanmamışsa**, bu ilke, parolanın **Oluşturulma tarihinden** itibaren geçerli tarihe kadar geçen gün sayısını hesaplar. Gün sayısı ayarladığınız eşiği aşarsa, parola ilkeyle uyumlu değil olarak işaretlenir.

### <a name="secrets-should-have-content-type-set-preview"></a>Gizli diziler içerik türü kümesine sahip olmalıdır (Önizleme)

Herhangi bir düz metin veya kodlanmış dosya, Anahtar Kasası gizli dizisi olarak depolanabilir. Ancak kuruluşunuz, parolalar, bağlantı dizeleri veya anahtar olarak depolanan sertifikalar üzerinde farklı döndürme ilkeleri ve kısıtlamalar ayarlamak isteyebilir. Bir içerik türü etiketi, bir kullanıcının gizli dizi değerini okumadan gizli bir nesnede nelerin depolandığını görme konusunda yardımcı olabilir. Bu ilkeyi, içerik türü etiketi ayarlanmış olmayan gizli dizileri denetlemek için kullanabilirsiniz. Bu ilkeyi, içerik türü etiketi ayarlanmış olmayan yeni parolaların oluşturulmasını engellemek için de kullanabilirsiniz.

### <a name="secrets-should-have-expiration-date-set-preview"></a>Gizli diziler sona erme tarihi ayarlanmalıdır (Önizleme)

Bu ilke, anahtar kasasındaki tüm gizli dizileri ve bir süre sonu tarihi uyumsuz olarak ayarlanan bayraklar gizli dizilerini denetler. Bu ilkeyi, sona erme tarihi kümesi olmayan gizli dizileri oluşturmayı engellemek için de kullanabilirsiniz.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Gizli dizileri, süre sonundan önce belirtilen gün sayısından daha fazla olmalıdır (Önizleme)

Gizli dizi sona erme için çok yakınsa, gizli anahtarı döndürmek için bir kuruluş gecikmesi bir kesinti oluşmasına neden olabilir. Parolaların, bir hataya tepki vermek için yeterli zaman sağlaması için, süresi dolmadan önce belirtilen gün sayısıyla döndürülmelidir. Bu ilke, sona erme tarihine kadar yakın olan gizli dizileri denetler ve bu eşiği gün cinsinden ayarlamanıza olanak sağlar. Bu ilkeyi Ayrıca, sona erme tarihine yakın yeni gizli dizileri oluşturmayı engellemek için de kullanabilirsiniz.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>Gizli diziler belirtilen en fazla geçerlilik süresine (Önizleme) sahip olmalıdır

Anahtar Kasanızda bir gizli dizi geçerli olabilecek maksimum süreyi gün cinsinden belirterek kurumsal uyumluluk gereksinimlerinizi yönetin. Ayarladığınız eşikten daha uzun olan gizlilikler uyumlu değil olarak işaretlenir. Bu ilkeyi Ayrıca, bir sona erme tarihi belirlediğiniz en uzun geçerlilik süresinden daha uzun süre ayarlanmış yeni parolaların oluşturulmasını engellemek için de kullanabilirsiniz.

---

## <a name="example-scenario"></a>Örnek Senaryo

100 sertifikalarını içeren birden fazla ekip tarafından kullanılan bir anahtar kasasını yönetirsiniz ve anahtar kasasındaki sertifikaların hiçbirinin 2 yıldan daha uzun bir süre geçerli olmadığından emin olmak istiyorsunuz.

1. **Sertifikaların belirtilen en yüksek geçerlilik süresi ilkesine sahip olması gerekir** , bir sertifikanın en fazla geçerlilik süresini 24 ay olacak şekilde belirtin ve ilkenin etkisini "Audit" olarak ayarlayın. 
1. [Uyumluluk raporu Azure Portal](#view-compliance-results)görüntülenir ve 20 sertifikanın uyumlu olduğunu ve > 2 yıl boyunca geçerli olduğunu ve kalan sertifikaların uyumlu olduğunu keşfedebilirsiniz. 
1. Bu sertifikaların sahiplerine başvurabilir ve sertifikaların 2 yıldan daha uzun süre geçerli olmadığı yeni güvenlik gereksinimini iletmezsiniz. Bazı takımlar yanıt verir ve 15 ' i, sertifikaların süresi 2 yıl veya daha az olan en fazla geçerlilik süresiyle yenilendi. Diğer takımlar yanıt vermez ve Anahtar Kasanızda hala 5 uyumlu olmayan sertifikanız vardır.
1. "Reddet" e atadığınız ilkenin etkisini değiştirirsiniz. 5 uyumlu olmayan sertifikalar iptal edilmez ve çalışmaya devam eder. Ancak, 2 yıldan büyük bir geçerlilik süresi ile yenilenemez. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Azure portal üzerinden Key Vault ilkesi etkinleştirme ve yönetme

### <a name="select-a-policy-definition"></a>Ilke tanımı seçin

1. Azure portalında oturum açın. 
1. Arama çubuğunda "Ilke" araması yapın ve **ilke**' yi seçin.

    ![Arama çubuğunu gösteren ekran görüntüsü.](../media/policy-img1.png)

1. Ilke penceresinde **tanımlar**' ı seçin.

    ![Tanımlar seçeneğini vurgulayan ekran görüntüsü.](../media/policy-img2.png)

1. Kategori filtresinde **Tümünü Seç** seçimini kaldırın ve **Key Vault** seçin. 

    ![Kategori filtresini ve seçili Key Vault kategorisini gösteren ekran görüntüsü.](../media/policy-img3.png)

1. Artık Azure Key Vault için genel önizlemeye sunulan tüm ilkeleri görebilmeniz gerekir. Yukarıdaki ilke kılavuzu bölümünü okuduğunuzdan ve anladığınızdan emin olun ve bir kapsama atamak istediğiniz ilkeyi seçin.  

    ![Genel önizleme için kullanılabilen ilkeleri gösteren ekran görüntüsü.](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Kapsama Ilke atama 

1. Uygulamak istediğiniz ilkeyi seçin, bu örnekte **sertifika geçerlilik süresini Yönet** ilkesi görüntülenir. Sol üst köşedeki ata düğmesine tıklayın.

    ![Sertifika geçerlilik süresini Yönet ilkesini gösteren ekran görüntüsü.](../media/policy-img5.png)
  
1. İlkenin uygulanmasını istediğiniz aboneliği seçin. Kapsamı, bir abonelik içinde yalnızca tek bir kaynak grubuyla kısıtlamayı tercih edebilirsiniz. İlkeyi aboneliğin tamamına uygulamak ve bazı kaynak gruplarını dışlamak istiyorsanız, bir dışlama listesi de yapılandırabilirsiniz. İlkeyi (denetim ya da reddetme) gerçekleşmesini veya **devre dışı** bırakmak (denetim veya reddetme) devre dışı bırakmak istiyorsanız ilke zorlama seçiciyi **etkin** olarak ayarlayın. 

    ![Kapsamı bir abonelik içinde yalnızca tek bir kaynak grubuyla kısıtlamayı tercih ettiğiniz gösteren ekran görüntüsü.](../media/policy-img6.png)

1. İstediğiniz en uzun geçerlilik süresini belirtmek için ekranın üst kısmındaki parametreler sekmesine tıklayın. Yukarıdaki bölümlerdeki kılavuzdan sonra ilke için **Denetim** veya **reddetme** seçimini yapın. Sonra gözden geçir + Oluştur düğmesini seçin. 

    ![En fazla geçerlilik süresini istediğiniz ay içinde belirtebileceğiniz Parametreler sekmesini gösteren ekran görüntüsü.](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Uyumluluk sonuçlarını görüntüle

1. Ilke dikey penceresine dönün ve Uyumluluk sekmesini seçin. Uyumluluk sonuçlarını görüntülemek istediğiniz ilke atamasını tıklatın.

    ![Uyumluluk sekmesini gösteren ve uyumluluk sonuçlarını görüntülemek istediğiniz ilke atamasını seçebileceğiniz ekran görüntüsü.](../media/policy-img8.png)

1. Bu sayfadan, sonuçları uyumlu veya uyumsuz kasaların filtrelemesine göre filtreleyebilirsiniz. Burada, ilke atamasının kapsamında uyumlu olmayan anahtar kasalarının bir listesini görebilirsiniz. Kasadaki bileşenlerden herhangi biri (Sertifikalar) uyumlu değilse, kasa uyumlu değil olarak kabul edilir. Bireysel olarak uyumlu olmayan bileşenleri (Sertifikalar) görüntülemek için tek bir kasa seçebilirsiniz. 


    ![İlke atamasının kapsamında uyumlu olmayan anahtar kasalarının bir listesini gösteren ekran görüntüsü.](../media/policy-img9.png)

1. Bir kasadaki, uyumsuz olmayan bileşenlerin adını görüntüleme


    ![Bir kasadaki, uyumsuz olmayan bileşenlerin adını görüntüleyebileceğiniz ekran görüntüsü.](../media/policy-img10.png)

1. Kullanıcıların Anahtar Kasası içinde kaynak oluşturma yeteneğinin engellenip engellenmeyeceğini denetlemeniz gerekiyorsa, istek sahibine ve isteklerin zaman damgalarına sahip reddedilen sertifika işlemlerinin özetini görüntülemek için **bileşen olayları (Önizleme)** sekmesine tıklayabilirsiniz. 


    ![Azure Key Vault nasıl çalıştığına genel bakış](../media/policy-img11.png)

## <a name="feature-limitations"></a>Özellik Sınırlamaları

Bir "reddetme" efektiyle ilke atanması, uyumlu olmayan kaynakların oluşturulmasını reddetmek için 30 dakika (ortalama Case) ve 1 saat (en kötü durum) gerektirebilir. Bir kasadaki mevcut bileşenlerin ilke değerlendirmesi, uyumluluk sonuçlarının Portal Kullanıcı arabiriminde görüntülenebilmesi için 1 saat (ortalama büyük/küçük harf) ve 2 saat (en kötü durum) kadar sürebilir. Uyumluluk sonuçları "başlatılmamış" olarak görünmüyorsa, bunun nedeni aşağıdakilerden biri olabilir:
- İlke değerlemesi henüz tamamlanmadı. İlk değerlendirme gecikmesi en kötü durum senaryosunda 2 saate kadar sürebilir. 
- İlke atamasının kapsamında herhangi bir anahtar kasası yok.
- İlke atamasının kapsamında sertifikalara sahip bir anahtar kasası yok.

> [!NOTE]
> Azure Key Vault gibi Azure Ilke [kaynağı sağlayıcı modları](../../governance/policy/concepts/definition-structure.md#resource-provider-modes), [bileşen uyumluluğu](../../governance/policy/how-to/get-compliance-data.md#component-compliance) sayfasında uyumluluk hakkında bilgi sağlar.

## <a name="next-steps"></a>Sonraki Adımlar

- [Azure ilke hizmeti](../../governance/policy/overview.md) hakkında daha fazla bilgi edinin
- Bkz. Key Vault örnekleri: [Key Vault yerleşik ilke tanımları](../../governance/policy/samples/built-in-policies.md#key-vault)
