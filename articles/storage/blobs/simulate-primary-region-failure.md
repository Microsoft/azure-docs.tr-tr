---
title: Öğretici-birincil bölgeden verileri okurken hata benzetimi yap
titleSuffix: Azure Storage
description: Depolama hesabı Okuma Erişimli Coğrafi bölge yedekli depolama (RA-GZRS) için yapılandırıldığında birincil bölgeden verileri okurken hata benzetimi yapın.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: devx-track-js
ms.openlocfilehash: 5a0bef4fbfd4ee9aa720dab430a33bbfcd0b918b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91280360"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Öğretici: birincil bölgeden verileri okurken hata benzetimi yap

Bu öğretici, bir dizinin ikinci bölümüdür. Bu durumda, hata benzetimi yaparak [Okuma Erişimli Coğrafi bölge-yedekli depolamanın](../common/storage-redundancy.md) (ra-GZRS) avantajları hakkında bilgi edinirsiniz.

Bir hatanın benzetimini yapmak için, [statik yönlendirme](#simulate-a-failure-with-an-invalid-static-route) veya [Fiddler](#simulate-a-failure-with-fiddler)kullanabilirsiniz. Her iki yöntem de, [Okuma Erişimli Coğrafi olarak yedekli](../common/storage-redundancy.md) (ra-GZRS) depolama hesabınızın birincil uç noktasına yönelik isteklerin hata benzetimi yapmanıza olanak sağlar. bunun yerine ikincil uç noktadan okumak üzere uygulamayı önde görürsünüz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

Serinin ikinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Uygulamayı çalıştırma ve duraklatma
> * [Geçersiz bir statik rota](#simulate-a-failure-with-an-invalid-static-route) veya [Fiddler](#simulate-a-failure-with-fiddler) ile hata benzetimi yapma
> * Birincil uç noktayı geri yükleme benzetimi gerçekleştirme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce, önceki öğreticiyi izleyin: [Azure depolama ile uygulama verilerinizi yüksek oranda kullanılabilir hale getirin][previous-tutorial].

Statik yönlendirmeyle bir hatanın benzetimini yapmak için yükseltilmiş bir komut istemi kullanacaksınız.

Fiddler kullanarak bir hatanın benzetimini yapmak için [Fiddler](https://www.telerik.com/download/fiddler) 'ı indirip yükleyin

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Geçersiz bir statik rota ile hata benzetimi yapma

[Okuma Erişimli Coğrafi olarak yedekli](../common/storage-redundancy.md) (ra-GZRS) depolama hesabınızın birincil uç noktasına yönelik tüm istekler için geçersiz bir statik yol oluşturabilirsiniz. Bu öğreticide, isteklerin depolama hesabına yönlendirilmesi için ağ geçidi olarak yerel ana bilgisayar kullanılır. Yerel ana bilgisayarın ağ geçidi olarak kullanılması, depolama hesabınızın birincil uç noktasına yönelik tüm isteklerin ana bilgisayara dönecek şekilde bir döngüye girmesine ve sonuçta başarısız olmasına yol açar. Geçersiz bir statik rota ile bir hata ve birincil uç noktayı geri yükleme benzetimi yapmak için aşağıdaki adımları izleyin.

### <a name="start-and-pause-the-application"></a>Uygulamayı başlatma ve duraklatma

Örneği başlatmak ve birincil depolamadan geldiğini onaylayan test dosyasını indirmek için [önceki öğreticideki][previous-tutorial] yönergeleri kullanın. Hedef platformunuza bağlı olarak, örneği el ile duraklatabilir veya bir istem sırasında bekleyebilirsiniz.

### <a name="simulate-failure"></a>Hata benzetimi yapma

Uygulama duraklatıldığında, Windows 'da yönetici olarak bir komut istemi açın veya Linux üzerinde kök olarak Terminal çalıştırın.

Bir komut istemine veya terminale aşağıdaki komutu girerek depolama hesabı birincil uç noktası etki alanı hakkında bilgi alın ve bunu `STORAGEACCOUNTNAME` depolama hesabınızın adıyla değiştirin.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Depolama hesabınızın IP adresini daha sonra kullanmak üzere bir metin düzenleyicisine kopyalayın.

Yerel ana bilgisayarın IP adresini almak için Windows komut isteminde `ipconfig` veya Linux terminalinde `ifconfig` yazın.

Bir hedef ana bilgisayar için statik bir yol eklemek üzere bir Windows komut isteminde veya Linux terminalinde aşağıdaki komutu yazın, `<destination_ip>` depolama HESABıNıZıN IP adresi ile ve `<gateway_ip>` yerel ana bilgisayar IP adresiniz ile değiştirin.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

Çalışan örneğe sahip pencerede, uygulamayı sürdürür veya örnek dosyayı indirmek için uygun anahtara basın ve ikincil depolama alanından geldiğini onaylayın. Daha sonra örneği yeniden duraklatabilir veya istem sırasında bekleyebilirsiniz.

### <a name="simulate-primary-endpoint-restoration"></a>Birincil uç noktayı geri yükleme benzetimi gerçekleştirme

Birincil uç noktanın yeniden işlev görmesinin benzetimini yapmak için, yönlendirme tablosundan geçersiz statik yolu silin. Bu, birincil uç noktaya yönelik tüm isteklerin varsayılan ağ geçidi üzerinden yönlendirilmesini sağlar. Bir Windows komut isteminde veya Linux terminalinde aşağıdaki komutu yazın.

#### <a name="linux"></a>Linux

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

Daha sonra uygulamayı sürdürebilir veya örnek dosyayı tekrar indirmek için ilgili anahtara basabilir, bu kez bir kez daha yeniden birincil depolamadan geldiğini onaylıyor.

## <a name="simulate-a-failure-with-fiddler"></a>Fiddler ile hata benzetimi yapma

Fiddler ile hata benzetimi yapmak için, RA-GZRS depolama hesabınızın birincil uç noktasına istekler için başarısız bir yanıt eklersiniz.

Aşağıdaki bölümler, Fiddler ile bir hata ve birincil uç nokta geri yüklemesinin benzetimini yapar.

### <a name="launch-fiddler"></a>Fiddler'ı açma

Fiddler’ı açıp **Kurallar**’ı ve **Kuralları Özelleştir**’i seçin.

![Fiddler kurallarını özelleştirme](media/simulate-primary-region-failure/figure1.png)

Fiddler ScriptEditor, **SampleRules.js** dosyasını başlatır ve görüntüler. Bu dosya, Fiddler’ı özelleştirmek için kullanılır.

Aşağıdaki kod örneğini, `OnBeforeResponse` depolama hesabınızın adıyla değiştirerek işlevine yapıştırın `STORAGEACCOUNTNAME` . Örneğe bağlı olarak, `HelloWorld` indirilmekte olan test dosyası adıyla (veya gibi bir ön ek) öğesini de değiştirmeniz gerekebilir `sampleFile` . Yeni kod, hemen çalıştırıldığından emin olmak için yorum yapılır.

Tamamlandıktan sonra, değişikliklerinizi kaydetmek için **Dosya** ' yı ve **Kaydet** ' i seçin. Aşağıdaki adımlarda kullanmak üzere ScriptEditor penceresini açık bırakın.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Özelleştirilmiş kuralı yapıştırma](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>Uygulamayı başlatma ve duraklatma

Örneği başlatmak ve birincil depolamadan geldiğini onaylayan test dosyasını indirmek için [önceki öğreticideki][previous-tutorial] yönergeleri kullanın. Hedef platformunuza bağlı olarak, örneği el ile duraklatabilir veya bir istem sırasında bekleyebilirsiniz.

### <a name="simulate-failure"></a>Hata benzetimi yapma

Uygulama duraklatıldığında, Fiddler 'a geri dönün ve işlevde kaydettiğiniz özel kuralın açıklamasını kaldırın `OnBeforeResponse` . Kuralın etkili olabilmesi için **Dosya** ' yı seçtiğinizden emin olun ve **değişikliklerinizi kaydedin.** Bu kod RA-GZRS depolama hesabına yönelik istekleri arar ve yol örnek dosyanın adını içeriyorsa, yanıt kodu döndürür `503 - Service Unavailable` .

Çalışan örneğe sahip pencerede, uygulamayı sürdürür veya örnek dosyayı indirmek için uygun anahtara basın ve ikincil depolama alanından geldiğini onaylayın. Daha sonra örneği yeniden duraklatabilir veya istem sırasında bekleyebilirsiniz.

### <a name="simulate-primary-endpoint-restoration"></a>Birincil uç noktayı geri yükleme benzetimi gerçekleştirme

Fiddler 'da özel kuralı kaldırın veya yeniden açıklama ekleyin. Kuralın artık etkin olmamasını sağlamak için **Dosya** ' yı ve **Kaydet** ' i seçin.

Çalışan örneğe sahip pencerede, uygulamayı yeniden deneyin veya örnek dosyayı indirmek için uygun anahtara basın ve bir kez daha, birincil depolama alanından geldiğini doğrulayın. Bundan sonra örnekten çıkabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Serinin ikinci bölümünde, Okuma Erişimli Coğrafi olarak yedekli depolamayı test etmek için bir hata benzetimi yapma hakkında bilgi edindiniz.

RA-GZRS depolamanın nasıl çalıştığı ve ilişkili riskleri hakkında daha fazla bilgi edinmek için aşağıdaki makaleyi okuyun:

> [!div class="nextstepaction"]
> [RA-GZRS ile HA uygulamaları tasarlama](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
