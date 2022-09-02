# Volume
 ![volume Resim](https://dominikbraun.io/static/img/docker-volumes.png)
 - Bilinidiği üzere container sisteminin bize sağladığı en büyük kolaylıklardan biri hızlı erişilebilir olmasıdır. Dolayısıyla sıklıkla containerlar silinir ve yerine yenisi oluşturulur bundan dolayı containerdan oluşan verilerin dışarıda saklanması gerekir ve bu sorunun çözümü Docker Volume dür. 
 Konteyneri ayağa kaldırırken volume parametresini belirttiğiniz zaman siz konteyneri sildiğiniz zaman volume silinmeyecektir ve dosyalarınız korunacaktır. Volume dosyaları `/var/lib/docker/volumes/` bu dizinde tutulur bu dizine ulaşıp konteynerin kullandığı dosyaları görebilirsiniz.
 
 - **`docker volume --help `**  -> volume bileşeni ile kullanabileceğimiz komutları görebiliriz.
 - Volüme kullanımı ```docker volume COMMAND``` şeklindedir. 
 - **`docker volume create <VolumeName>`** -> parametre olarak verilen isimde bir volume oluşturur.
 - **`docker volume ls`** -> Sistemdeki mevcut Volume'leri listeler.
 - **`docker volume rm <VolumeName>`** -> ID'si verilen Volume'ü siler.
 - **`docker volume inspect <VolumeName>`** -> volume detaylarını verir.
 - `docker container run -it -v ilkvolume:/uygulama alpine sh` örneğinde alpine imajından bir container oluşturuyoruz ve bu container'ı `-v` ile ilkvolume adındaki volume'u container içerisindeki /uygulama adındaki klasöre bağlıyoruz.
    - Burada ki /uygulama klasörü eğer ki container içerisinde mevcut değilse otomatik olarak oluşturulur.
 - Tek bir Volume'ü yukarı daki örnek deki gibi birden fazla container'a bağlayabiliriz.
### Docker Boş-Dolu Volume Davranışları
- Container içerisinde volume adında bir klasör yoksa, o isimde bir klasör oluşturulur ve içerisine mount edilir. Peki volume'leri bir klasör içerisine mount etmek istersek?
- Eğer volume imaj içinde bulunan mevcut klasöre mount edilirse:
  - Klasör boşsa o anda volume içinde hangi dosyalar varsa bu klasörde de o dosyaları görürüz
  - Klasörde dosya varsa ve volume boşsa klasördeki **dosyalar volume'e kopyalanır**
  - Klasörde dosya var ya da yok fakat volume boş değilse, klasörün içinde volume'de ne varsa onu görürüz
  
  ![**https://docs.docker.com/storage/bind-mounts/**](https://docs.docker.com/storage/images/types-of-mounts-bind.png)
  
### Bind Mounts
- Host makinada bulunan bir klasörü veya dosyayı Container içerisinde map etmeye Bind Mounts denir.
- `docker container run -d -p 80:80 --name web1 nginx` -> komutu ile nxginx imajından web1 isimli bir web sitesi oluşturduk.
- Nginx imajı oluşturulan web sitesinde /usr/share/nginx/html bulunan html dosyasını kullanır. Eğer ki biz Bind Mounts methodu ile kendi host makinamızda  bir html dosyasını nginx imajından oluşturacağımız containera mount edersek oluşturulan web sayfasında bizim html dosyamız kullanılır. Örneğin;
- `docker container run -d -p 80:80 -v C:/Users/MERT/webhtml:/usr/share/nginx/html nginx` oluşturulan container'da artık nginx'in kendi html dosyası yerine bizim oluşturduğumuz webhtml dosyası kullanılır.
- Fakat bu yöntem development aşamasında kullanılmalı, kesinlikle production ortamında kullanmamalıyız çünkü görüldüğü üzere C:/Users/MERT/webhtml yalnızca benim host makinamda bulunan bir dosyadır.
