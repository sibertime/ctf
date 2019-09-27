# STCTF#1

![](https://raw.githubusercontent.com/sibertime/sibertime-vulnerable-web-application/master/img/readme/logo.png)

## Writeup

**İlk önce CTF sorusunu local dizinimize indiriyoruz.**
> "sibertime/ctf.git", [Link](https://github.com/sibertime/ctf.git)

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/1.png)

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/2.png)

**İndirilen zip dosyası içerisindeki dosyayı dışarı çıkartmak istediğimizde bizden şifre isteniyor.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/3.png)

**Şifreyi kırmak için internette bulunan toollardan faydalanabilirsiniz. Biz bu adımda aşağıda yer alan toolu kullanacağız.**
> "zipBruteForce.py", [Link](https://github.com/ferhatcil/tool-s/blob/master/zipBruteForce.py)

**Wget yardımı ile toolu indirebilirsiniz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/4.png)

**zipBruteForce.py ile zip şifresinin zelda09% olduğunu öğrendik.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/5.png)

**Bulunan parola ile zip içindeki dosyayı çıkartıyoruz. Flag.txt yi okuduktan sonra içinde base64'lü iki string bulunduğunu görüyoruz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/6.png)

**İlgili stringleri terminal üzerinden veya online base64 decoderları kullanarak çözümleyebilirsiniz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/7.png)

**Çözümlenen stringlerden iki adet link çıkmaktadır ve bu linklerin ikiside aynı dosyayı barındırmaktadır. Herhangi bir linke tıklayarak dosyayı localinize indirebilirsiniz. İndirilen dosya bir ram imajı olduğu için volatility aracı ile analiz edebilirsiniz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/8.png)

> "volatility -f ctf.raw imageinfo"

**komutu ile imajdaki işletim sistemini tespit ediyoruz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/9.png)

**işletim sistemimizi belirledikten sonra --profile yanına pslist komutunu girerek çalışan procesleri listeyebiliriz.**
> "volatility -f ctf.raw --profile Win7SP1x64"

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/10.png)

**Çalışan processler arasında notepad processi bulunmaktadır. İlgili processi indirmek için;**
> "volatility -f ctf.raw memdump --profile Win7SP1x64 -p 712 --dump-dir=./"

**komutunu kullanmamız yeterli olacaktır.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/11.png)

**İndirilen dosya içinde "Flag" kelimesini aratıyoruz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/12.png)

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/13.png)

**Çıkan sonuçlarda yer alan github linkine gidiyoruz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/14.png)

**İligli link üzerinde yer alan commitleri incelediğimiz zaman önceden yüklenen bir dosyayının silindiğini görüyoruz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/15.png)

**Silinen dosyanın içeriği;**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/17.png)

**Wget komutu ile ilgili dosyayı localimize indiriyoruz.**
> "wget ", [Link](https://raw.githubusercontent.com/ferh4t/F41DD86F46084ACF4CB1A77087C93D1F19737A5B185269EF0D1A4E9A93A9A94CA559051C3035B3789ED3F86B0813919E294B/324151f3cb2ab4c930c57aca7e929d736074c723/test.py)

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/18.png)

**İndirilen python kodu ilk bakışta güç durumda, iligli kod  sadeleştiriyoruz.**
**İlk önce hashli kısımları çözümlemeye başlıyoruz. Hashler ilk önce hex daha sonra base64 ile encode edilmiştir. Kodun temiz haline ulaşmak için şifreli kısımları decode ediyoruz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/19.png)

**Kodumuz bu hale geldi. Daha temiz değil mi? Ama henüz bitmedi. Artık kodda yapmamız gereken maps stringinde yer alan sayıları alphabet dizinindeki indisler ile eşleştirmek olacak. İlgili eşleştirmeyi yaptıktan sonraki adımımız bir web uygulaması olan 167.71.40.74 oldu.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/21.png)

**İlgili adresi ziyaret ediyoruz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/22.png)

**Açılan sayfada kaynak kodlarına baktığımız zaman bize bir ipucu vermektedir. Bu ipucu, ilgili login.php'nin kaynak kodlarının hashli bir uzantı ile web sunucunda yer aldığıdır.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/23.png)

**Uzantımız "ctf" ti, frontend tarafında kalın font ile yazmamız, ctf çok sevdiğimizi söylememizden dolayı CTF olacaktı. ".ctf" uzantısı ile beraber sayfayı ziyaret ettiğimizde sayfanın kaynak kodları karşımızdaydı.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/24.png)

**Kaynak kodları okunduğu zaman girilen kullanıcıyı veritabanından bul kullanıcı parolasını veritabanından al ve girilen parolayla eşit olup olmadığını kontrol et, eşitse oturum aç.**

> $db->prepare("SELECT password FROM users WHERE username='" . $username . "'");

**sorgusu eğer veritabanına kayıtlı bir kullanıcı varsa ilgili kullanıcının parolasını yoksa NULL değer döndürecektir.**

	<?php
        if ($result == $password) {
                    $_SESSION['ctf'] = 'flag';
                    header("Location: fl4g.php", true, 301);
                }
	?>

**Yukarıdaki kod bloğuna dikkat ettiğimiz zaman veritabanından gelen parola($result) ile bizim girdiğimiz parolayı karşılaştırmaktadır. Eğer burada if bloğunu true sonuç döndürebilirsek bayrağa erişmiş olacağız. Yani ya veritabanından bir kullanıcı tahmin edeceğiz ya da if bloğunu manipüle edeceğiz.**

**Veritabanında olmayan bir kullanıcı adı girdiğimizde $result değişkeni Null değerini almaktadır. İlgili Null değerine karşılık şifre olarak 0 girdiğimizde sonuç true dönecekti ve bayrağa ulaşmış olacaktık. Pekiştirmek için aşağıda yer alan resmi inceleyebilirsiniz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/25.png)

**asdfg ve 0 verilerini girdikten sonra bizi bir resim karşılamaktaydı bu resmi localimize indirelim.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/26.gif)

**Resim içerisine gizlenen bir şey olup olmadığını resmin orjinal halini google üzerinden aratarak iki resim arasında boyut arşılaştırması yaparak anlayabilirdik.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/27.png)

**resim.jpg google üzerinden indirilen temiz görseldir.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/28.png)

**Resim içerisinde steghide yardımı ile gizlenmiş bir dosya bulunmaktaydı. Resim içerisinde yer alan dosyayı dışarı çıkarmak istediğimizde bizden parola istemekteydi. Dosyayı çıkartmak için steghide için özel olarak kodlanmış brute force araçları bulunmaktadır.**

> "Bu araçlardan birisi", [Link](https://github.com/Va5c0/Steghide-Brute-Force-Tool)

**Aracı local dizinimize indiriyoruz**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/29.png)

**Aracı indirdikten sonra resmi ve şifre listesini belirtmemiz gerekiyordu burdaki şifre listesi. Github üzerine yüklediğimiz st-pswd-list olacaktı. Şifreyi kırma işlemine başladıktan sonra şifremiz "1453fatihinistanbulu" olarak karşımıza çıktı.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/30.png)

**Şifre bulunduktan sonra resim içersindeki dosyayı dışarıya alıyoruz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/31.png)

**Dışarı alınan dosyayı hexeditor ile açtığımız zaman bunun bir 7z dosyası olduğunu anlamamız için bize bırakılan 0z kısmına dikkat etmemiz gerekiyordu. :)**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/32.png)

**hexeeditor ile dosyayı 7z formatına getirip içerisinde yer alan dosyayı dışarı çıkartırıyoruz.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/33.png)

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/34.png)

**Dosya içerisinden binary kodları çıkmaktadır.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/35.png)

**İlgili komutları online bir araç olan https://codebeautify.org/binary-to-text ile decode ettiğimiz zaman flag karşımızda olacaktı.**

![](https://raw.githubusercontent.com/sibertime/ctf/master/writeup/images/36.png)

## *Soruyu çözenler*
- eybisi
- Ferit '𝘼𝙉𝙏𝙃𝘼' Uzun
