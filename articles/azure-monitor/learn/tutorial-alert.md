---
title: Azure Application Insights uyarı gönder | Microsoft Docs
description: Azure Application Insights kullanarak uygulamanızdaki hatalara yanıt olarak uyarı gönderme öğreticisi.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 370d86ae28e49bba9681c6bdc81cc05b4e12a97b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894867"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Azure Application Insights ile uygulama durumunu izleme ve uyarma

Azure Application Insights, uygulamanızı izlemenizi ve kullanılabilir olduğunda, hatalardan karşılaşmanız veya performans sorunlarından olumsuz bir sorun yaşamanız durumunda size uyarı göndermenizi sağlar.  Bu öğretici, uygulamanızın kullanılabilirliğini sürekli olarak denetlemek için testler oluşturma sürecinde size kılavuzluk eder.

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Uygulamanın yanıtını sürekli denetlemek için kullanılabilirlik testi oluşturma
> * Sorun oluştuğunda yöneticilere e-posta gönder

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

[Application Insights kaynağı](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights)oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-availability-test"></a>Kullanılabilirlik testi oluşturma

Application Insights ' deki kullanılabilirlik testleri, uygulamanızı dünyanın dört bir yanındaki çeşitli konumlardan otomatik olarak sınamanızı sağlar.   Bu öğreticide, Web uygulamanızın kullanılabilir olduğundan emin olmak için bir URL testi gerçekleştirirsiniz.  Ayrıca, ayrıntılı işlemini test etmek için kapsamlı bir yol da oluşturabilirsiniz. 

1. **Application Insights**’ı ve sonra aboneliğinizi seçin.  

2. **Araştır** menüsünde **kullanılabilirlik** ' i seçin ve ardından **Test oluştur**' a tıklayın.

    ![Kullanılabilirlik testi Ekle](media/tutorial-alert/add-test-001.png)

3. Test için bir ad yazın ve diğer varsayılan değerleri bırakın.  Bu seçim, beş farklı coğrafi konumdan her 5 dakikada bir uygulama URL 'si için istekleri tetikler.

4. Test başarısız olursa, yanıt verme ayrıntılarını tanımlayabileceğiniz **Uyarılar** açılan listesini açmak için **Uyarılar** ' ı seçin. **Neredeyse gerçek zamanlı** ' ı seçin ve durumu etkin olarak ayarlayın **.**

    Uyarı ölçütleri karşılandığında gönderilecek bir e-posta adresi yazın.  İsteğe bağlı olarak, uyarı ölçütleri karşılandığında çağrılacak bir Web kancası adresini yazabilirsiniz.

    ![Test oluştur](media/tutorial-alert/create-test-001.png)

5. Test paneline dönün, üç nokta simgesini seçin ve uyarıyı Düzenle ' yi seçerek en gerçek zamanlı uyarınızın yapılandırmasını girin.

    ![Uyarıyı Düzenle](media/tutorial-alert/edit-alert-001.png)

6. Başarısız konumları 3 ' e eşit veya daha büyük olarak ayarlayın. Uyarı eşiğinden ihlal edildiğinde kimin bildirim almasını sağlayan bir [eylem grubu](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) oluşturun.

    ![Uyarı Kullanıcı arabirimini Kaydet](media/tutorial-alert/save-alert-001.png)

7. Uyarınızı yapılandırdıktan sonra, her bir konumdan ayrıntıları görüntülemek için test adına tıklayın. Testler, belirli bir zaman aralığının başarısını/başarısızlıklarını görselleştirmek için hem çizgi grafiğinde hem de dağılım çizim biçiminde görüntülenebilir.

    ![Test ayrıntıları](media/tutorial-alert/test-details-001.png)

8. Dağılım grafiğindeki üzerine tıklayarak herhangi bir testin ayrıntılarına gidebilirsiniz. Bu, uçtan uca işlem ayrıntıları görünümünü başlatacaktır. Aşağıdaki örnekte, başarısız bir istek için Ayrıntılar gösterilmektedir.

    ![Test sonucu](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Sonraki adımlar

Sorunları nasıl uyarabileceğinizi öğrendiğinize göre, kullanıcıların uygulamanızla nasıl etkileşim kurduğuna ilişkin daha fazla bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Kullanıcıları anlama](../../azure-monitor/learn/tutorial-users.md)