---
title: Azure Data Box için müşteri tarafından yönetilen anahtarları yönetmek üzere Azure portal kullanın
description: Azure Data Box için Azure Key Vault ile müşteri tarafından yönetilen anahtarlar oluşturup yönetmek üzere Azure portal nasıl kullanacağınızı öğrenin. Müşteri tarafından yönetilen anahtarlar, erişim denetimleri oluşturmanıza, döndürmenize, devre dışı bırakmanızı ve iptal etmenize olanak tanır.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: e6b588ddea5bf4b4c92e89d9cebb37b09b9a86af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791553"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Azure Key Vault Azure Data Box için müşteri tarafından yönetilen anahtarları kullanın

Azure Data Box, bir şifreleme anahtarı aracılığıyla bir cihazı kilitlemek için kullanılan cihaz kilit açma anahtarını (cihaz parolası olarak da bilinir) korur. Bu şifreleme anahtarı varsayılan olarak Microsoft tarafından yönetilen bir anahtardır. Ek denetim için, müşteri tarafından yönetilen bir anahtar kullanabilirsiniz.

Müşterinin yönettiği bir anahtarın kullanılması, cihazdaki verilerin nasıl şifrelendiğini etkilemez. Yalnızca cihaz kilit açma anahtarının nasıl şifrelendiğini etkiler.

Bu denetim düzeyini sipariş sürecinde tutmak için, siparişinizi oluştururken müşteri tarafından yönetilen bir anahtar kullanın. Daha fazla bilgi için bkz. [öğretici: Order Azure Data Box](data-box-deploy-ordered.md).

Bu makalede, [Azure Portal](https://portal.azure.com/)mevcut Data Box siparişiniz için müşteri tarafından yönetilen bir anahtarın nasıl etkinleştirileceği gösterilmektedir. Müşteri tarafından yönetilen geçerli anahtarınız için anahtar kasasını, anahtarı, sürümü veya kimliği değiştirmeyi veya Microsoft tarafından yönetilen anahtarı kullanmaya geri geçiş yapılacağını öğreneceksiniz.

Bu makale Azure Data Box ve Azure Data Box Heavy cihazları için geçerlidir.

## <a name="requirements"></a>Gereksinimler

Data Box bir sıra için müşteri tarafından yönetilen anahtar aşağıdaki gereksinimleri karşılamalıdır:

- Anahtarın, **geçici silme** ve **Temizleme** etkin olan bir Azure Key Vault oluşturulması ve depolanması gerekir. Daha fazla bilgi için bkz. [Azure Key Vault nedir?](../key-vault/general/overview.md) Siparişinizi oluştururken veya güncelleştirirken bir anahtar kasası ve anahtar oluşturabilirsiniz.

- Anahtar, 2048 boyutundaki veya daha büyük bir RSA anahtarı olmalıdır.

## <a name="enable-key"></a>Anahtarı etkinleştir

Azure portal mevcut Data Box siparişiniz için müşteri tarafından yönetilen bir anahtarı etkinleştirmek üzere aşağıdaki adımları izleyin:

1. Data Box siparişiniz için **genel bakış** ekranına gidin.

    ![Data Box siparişin genel bakış ekranı-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. **Ayarlar > şifreleme**' ye gidin ve **müşteri tarafından yönetilen anahtar**' ı seçin. Sonra **anahtar ve Anahtar Kasası Seç '** i seçin.

    ![Müşteri tarafından yönetilen anahtar şifreleme seçeneğini belirleyin](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   **Azure Key Vault anahtar seçin** ekranında, aboneliğiniz otomatik olarak doldurulur.

 3. **Anahtar Kasası** için, açılan listeden var olan bir anahtar kasasını seçebilir veya **Yeni oluştur** ' u seçip yeni bir Anahtar Kasası oluşturabilirsiniz.

     ![Müşteri tarafından yönetilen anahtar seçerken Anahtar Kasası seçenekleri](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     Yeni bir Anahtar Kasası oluşturmak için, **Yeni Anahtar Kasası oluştur** ekranına abonelik, kaynak grubu, Anahtar Kasası adı ve diğer bilgileri girin. **Kurtarma seçenekleri**' nde, **geçici silme** ve **Temizleme korumasının** etkinleştirildiğinden emin olun. Ardından **gözden geçir + oluştur**' u seçin.

      ![Azure Key Vault gözden geçirin ve oluşturun](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      Anahtar kasanızın bilgilerini gözden geçirin ve **Oluştur**' u seçin. Anahtar Kasası oluşturma işleminin tamamlanabilmesi için birkaç dakika bekleyin.

       ![Ayarlarınızla Azure Key Vault oluşturma](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. **Azure Key Vault anahtar seçin** ekranında, anahtar kasasından mevcut bir anahtarı seçebilir veya yeni bir anahtar oluşturabilirsiniz.

    ![Azure Key Vault anahtardan anahtar seçin](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   Yeni bir anahtar oluşturmak istiyorsanız **Yeni oluştur**' u seçin. Bir RSA anahtarı kullanmanız gerekir. Boyut, 2048 veya daha büyük olabilir.

    ![Azure Key Vault yeni anahtar oluştur](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    Yeni anahtarınız için bir ad girin, diğer varsayılanları kabul edin ve **Oluştur**' u seçin. Anahtar Kasanızda bir anahtarın oluşturulduğunu size bildirirsiniz.

    ![Yeni anahtarı Adlandır](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. **Sürüm** için, açılan listeden var olan bir anahtar sürümünü seçebilirsiniz.

    ![Yeni anahtar için sürüm seçin](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    Yeni bir anahtar sürümü oluşturmak istiyorsanız **Yeni oluştur**' u seçin.

    ![Yeni bir anahtar sürümü oluşturmak için bir iletişim kutusu açın](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    Yeni anahtar sürümüne ilişkin ayarları seçin ve **Oluştur**' u seçin.

    ![Yeni bir anahtar sürümü oluştur](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. Bir Anahtar Kasası, anahtar ve anahtar sürümünü seçtiğinizde **Seç**' i seçin.

    ![Azure Key Vault bir anahtar](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    **Şifreleme türü** ayarları, seçtiğiniz anahtar kasasını ve anahtarı gösterir.

    ![Müşteri tarafından yönetilen anahtar için anahtar ve Anahtar Kasası](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. Bu kaynak için müşteri tarafından yönetilen anahtarı yönetmek üzere kullanılacak kimlik türünü seçin. Sipariş oluşturma sırasında oluşturulan **sistem tarafından atanan** kimliği kullanabilir veya Kullanıcı tarafından atanan bir kimlik seçebilirsiniz.

    Kullanıcı tarafından atanan kimlik, kaynaklara erişimi yönetmek için kullanabileceğiniz bağımsız bir kaynaktır. Daha fazla bilgi için bkz. [yönetilen kimlik türleri](../active-directory/managed-identities-azure-resources/overview.md).

    ![Kimlik türünü seçin](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    Kullanıcı kimliği atamak için **Kullanıcı atandı**' ı seçin. Ardından, **Kullanıcı kimliği Seç**' i seçin ve kullanmak istediğiniz yönetilen kimliği seçin.

    ![Kullanılacak kimliği seçin](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    Burada yeni bir kullanıcı kimliği oluşturamazsınız. Bir oluşturma hakkında bilgi edinmek için, bkz. [Azure Portal kullanarak Kullanıcı tarafından atanan yönetilen kimlik için rol oluşturma, listeleme, silme veya atama](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

    Seçilen Kullanıcı kimliği, **şifreleme türü** ayarlarında gösterilir.

    ![Şifreleme türü ayarlarında gösterilen seçili kullanıcı kimliği](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. Güncelleştirilmiş **şifreleme türü** ayarlarını kaydetmek için **Kaydet** ' i seçin.

     ![Müşteri tarafından yönetilen anahtarınızı kaydetme](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    Anahtar URL 'SI, **şifreleme türü** altında görüntülenir.

    ![Müşteri tarafından yönetilen anahtar URL 'SI](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>Anahtarı Değiştir

Kullanmakta olduğunuz müşteri tarafından yönetilen anahtar için Anahtar Kasası, anahtar ve/veya anahtar sürümünü değiştirmek için şu adımları izleyin:

1. Data Box siparişiniz için **genel bakış** ekranında **Ayarlar**  >  **şifreleme**' ye gidin ve **anahtarı Değiştir**' e tıklayın.

    ![Müşteri tarafından yönetilen anahtar-1 Data Box siparişin genel bakış ekranı](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. **Farklı bir anahtar kasası ve anahtar seçin** öğesini seçin.

    ![Data Box sıranın genel bakış ekranı, farklı bir anahtar ve Anahtar Kasası seçeneği seçin](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. **Anahtar kasasından seçim anahtarı** ekranında abonelik gösterilir ancak Anahtar Kasası, anahtar veya anahtar sürümü yoktur. Aşağıdaki değişikliklerden herhangi birini yapabilirsiniz:

   - Aynı anahtar kasasından farklı bir anahtar seçin. Anahtar ve sürümü seçmeden önce anahtar kasasını seçmeniz gerekir.

   - Farklı bir Anahtar Kasası seçin ve yeni bir anahtar atayın.

   - Geçerli anahtarın sürümünü değiştirin.
   
    Değişikliklerinizi bitirdiğinizde **Seç**' i seçin.

    ![Şifreleme seçeneğini belirleyin-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. **Kaydet**’i seçin.

    ![Güncelleştirilmiş şifreleme ayarlarını kaydet-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>Kimliği Değiştir

Bu sipariş için müşteri tarafından yönetilen anahtara erişimi yönetmek üzere kullanılan kimliği değiştirmek için şu adımları izleyin:

1. Tamamlanan Data Box siparişiniz için **genel bakış** ekranında **Ayarlar**  >  **şifreleme**' ye gidin.

2. Aşağıdaki değişikliklerden birini yapın:

     - Farklı bir kullanıcı kimliğine geçmek için **farklı bir kullanıcı kimliği Seç**' e tıklayın. Ardından, ekranın sağ tarafındaki panelde farklı bir kimlik seçin ve **Seç**' i seçin.

       ![Müşteri tarafından yönetilen anahtar için Kullanıcı tarafından atanan kimliği değiştirme seçeneği](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - Sipariş oluşturma sırasında oluşturulan sistem tarafından atanan kimliğe geçiş yapmak için, **Select Identity Type** tarafından **atanan sistem** ' i seçin.

     ![Müşteri tarafından yönetilen anahtar için bir sisteme geçiş seçeneği](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. **Kaydet**’i seçin.

    ![Güncelleştirilmiş şifreleme ayarlarını kaydet-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Microsoft yönetilen anahtarını kullan

Müşteri tarafından yönetilen bir anahtarı kullanarak siparişiniz için Microsoft tarafından yönetilen bir anahtarla değiştirmek için aşağıdaki adımları izleyin:

1. Tamamlanan Data Box siparişiniz için **genel bakış** ekranında **Ayarlar**  >  **şifreleme**' ye gidin.

2. Tür ' i **seçin**, **Microsoft yönetilen anahtarı**' nı seçin.

    ![Data Box siparişin genel bakış ekranı-5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. **Kaydet**’i seçin.

    ![Microsoft tarafından yönetilen anahtar için güncelleştirilmiş şifreleme ayarlarını kaydet](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>Sorun giderme hataları

Müşteri tarafından yönetilen anahtarınızla ilgili herhangi bir hata alırsanız, sorunlarını gidermek için aşağıdaki tabloyu kullanın.

| Hata kodu| Hata ayrıntıları| Gider?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Müşteri tarafından yönetilen anahtar devre dışı bırakıldığından geçiş anahtarı getirilemedi.| Evet, anahtar sürümünü etkinleştirerek.|
| Ssemusererrorencryptionkeyerermedi| Müşteri tarafından yönetilen anahtarın süresi geçtiğinden geçiş anahtarı getirilemedi.| Evet, anahtar sürümünü etkinleştirerek.|
| Ssemusererrorkeyayrıntılar NotFound| Müşteri tarafından yönetilen anahtar bulunamadığı için geçiş anahtarı getirilemedi.| Anahtar kasasını sildiyseniz, müşteri tarafından yönetilen anahtarı kurtaramazsınız.  Anahtar kasasını farklı bir kiracıya geçirdiyseniz, [abonelik taşıdıktan sonra Anahtar Kasası KIRACı kimliğini değiştirme](../key-vault/general/move-subscription.md)konusuna bakın. Anahtar kasasını sildiyseniz:<ol><li>Evet, temizleme koruma süresi içinde ise, [anahtar kasasını kurtarma](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)bölümündeki adımları kullanarak.</li><li>Temizleme koruma süresinin ötesinde, hayır.</li></ol><br>Diğer bir deyişle, Anahtar Kasası bir kiracı geçişi gerçekleştirmişse, aşağıdaki adımlardan biri kullanılarak kurtarılabilir: <ol><li>Anahtar kasasını eski kiracıya geri döndürür.</li><li>`Identity = None`Değerini ayarlayın ve ardından değerine geri ayarlayın `Identity = SystemAssigned` . Bu, yeni kimlik oluşturulduktan sonra kimliği siler ve yeniden oluşturur. `Get` `Wrap` `Unwrap` Anahtar kasasının erişim ilkesindeki yeni kimlik için, ve izinlerini etkinleştirin.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | Müşteri tarafından yönetilen anahtar uygulandı, ancak anahtar erişimi verilmedi ya da iptal edildi veya güvenlik duvarı etkinleştirilmediği için anahtar kasasına erişilemiyor. | Müşteri tarafından yönetilen anahtara erişimi etkinleştirmek için anahtar kasanıza Seçilen kimliği ekleyin. Anahtar Kasası güvenlik duvarı etkinleştirilmişse, bir sistem tarafından atanan kimliğe geçin ve ardından bir müşteri tarafından yönetilen anahtar ekleyin. Daha fazla bilgi için bkz. [anahtarı etkinleştirme](#enable-key). |
| Ssemusererrorkeyvaultayrıntılar NotFound| Müşteri tarafından yönetilen anahtar için ilişkili anahtar kasası bulunamadığı için geçiş anahtarı getirilemedi. | Anahtar kasasını sildiyseniz, müşteri tarafından yönetilen anahtarı kurtaramazsınız.  Anahtar kasasını farklı bir kiracıya geçirdiyseniz, [abonelik taşıdıktan sonra Anahtar Kasası KIRACı kimliğini değiştirme](../key-vault/general/move-subscription.md)konusuna bakın. Anahtar kasasını sildiyseniz:<ol><li>Evet, temizleme koruma süresi içinde ise, [anahtar kasasını kurtarma](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)bölümündeki adımları kullanarak.</li><li>Temizleme koruma süresinin ötesinde, hayır.</li></ol><br>Diğer bir deyişle, Anahtar Kasası bir kiracı geçişi gerçekleştirmişse, aşağıdaki adımlardan biri kullanılarak kurtarılabilir: <ol><li>Anahtar kasasını eski kiracıya geri döndürür.</li><li>`Identity = None`Değerini ayarlayın ve ardından değerine geri ayarlayın `Identity = SystemAssigned` . Bu, yeni kimlik oluşturulduktan sonra kimliği siler ve yeniden oluşturur. `Get` `Wrap` `Unwrap` Anahtar kasasının erişim ilkesindeki yeni kimlik için, ve izinlerini etkinleştirin.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Müşteri tarafından yönetilen anahtar bulunamadığı için geçiş anahtarı getirilemedi.| Evet, aşağıdakileri denetle: <ol><li>Anahtar Kasası hala MSI 'ye erişim ilkesinde sahiptir.</li><li>Kimlik, atanan sistem türünde.</li><li>Anahtar kasasının erişim ilkesindeki kimlik için Get, Wrap ve Unwrap izinlerini etkinleştirin.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | Eklenebilecek Kullanıcı tarafından atanan kimliklerin toplam sayısı sınırına ulaştığınızdan, Yeni Kullanıcı tarafından atanan kimlik ekleme işlemi başarısız oldu. | Yeniden denemeden önce lütfen daha az Kullanıcı kimliği ile işlemi yeniden deneyin veya kaynaktan bazı Kullanıcı tarafından atanan kimlikleri kaldırın. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Yönetilen kimlik erişimi işlemi başarısız oldu. <br> Note: abonelik farklı bir kiracıya taşındığında bu senaryo için de budur. Müşterinin kimliği el ile yeni kiracıya taşıması vardır. Daha fazla bilgi için PFA postası. | Lütfen Seçilen kimliği aboneliğin bulunduğu yeni kiracıya taşıyın. Daha fazla bilgi için bkz. [anahtarı etkinleştirme](#enable-key). |
| Ssemusererrorkekuserıdentitynotfound | Müşteri tarafından yönetilen anahtar uygulandı, ancak anahtara erişimi olan kullanıcı tarafından atanan kimlik Active Directory 'de bulunamadı. <br> Note: Bu, Kullanıcı kimliğinin Azure 'dan silindiği durumdur.| Müşteri tarafından yönetilen anahtara erişimi etkinleştirmek için lütfen anahtar kasanıza seçilen farklı bir kullanıcı atanmış kimliği eklemeyi deneyin. Daha fazla bilgi için bkz. [anahtarı etkinleştirme](#enable-key). |
| Ssemusererroruseratandidentityabsent | Müşteri tarafından yönetilen anahtar bulunamadığı için geçiş anahtarı getirilemedi. | Müşterinin yönettiği anahtara erişilemedi. Anahtarla ilişkili kullanıcı tarafından atanan kimlik (UAı) silinmiş veya UAı türü değişmiş. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Yönetilen kimlik erişimi işlemi başarısız oldu. <br> Note: abonelik farklı bir kiracıya taşındığında bu senaryo için de budur. Müşterinin kimliği el ile yeni kiracıya taşıması vardır. Daha fazla bilgi için PFA postası. | Müşteri tarafından yönetilen anahtara erişimi etkinleştirmek için lütfen anahtar kasanıza seçilen farklı bir kullanıcı atanmış kimliği eklemeyi deneyin. Daha fazla bilgi için bkz. [anahtarı etkinleştirme](#enable-key).|
| SsemUserErrorKeyVaultBadRequestException | Müşteri tarafından yönetilen anahtar uygulandı, ancak anahtar erişimi verilmedi ya da iptal edildi veya güvenlik duvarı etkinleştirilmediği için anahtar kasasına erişilemiyor. | Müşteri tarafından yönetilen anahtara erişimi etkinleştirmek için anahtar kasanıza Seçilen kimliği ekleyin. Anahtar Kasası güvenlik duvarı etkinleştirilmişse, bir sistem tarafından atanan kimliğe geçin ve ardından bir müşteri tarafından yönetilen anahtar ekleyin. Daha fazla bilgi için bkz. [anahtarı etkinleştirme](#enable-key). |
| Genel hata  | Geçiş anahtarı getirilemedi.| Bu genel bir hatadır. Hatayı gidermek ve sonraki adımları öğrenmek için Microsoft Desteği başvurun.|

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault nedir?](../key-vault/general/overview.md)
- [Hızlı başlangıç: Azure portalı kullanarak Azure Key Vault'tan gizli dizi ayarlama ve alma](../key-vault/secrets/quick-create-portal.md)
