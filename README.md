本文說明如何使用[Apache Ant](http://ant.apache.org/)預先編譯jsp檔案。透過預先載入jsp的方式，可增加web應用的效能。

## tomcat如何編譯jsp
在說明預先編譯jsp以前，須要先了解tomcat如何對jsp進行編譯的。
![how tomcat handle jsp](https://media.licdn.com/mpr/mpr/shrinknp_800_800/AAEAAQAAAAAAAAYZAAAAJDFjNTNlNWY5LTA1M2YtNDYyYy1hN2YwLTU1N2I1MWEzZTk5ZA.png)  
如上圖所示，當使用者對jsp發出請求的時候，tomcat使用[Jasper2](https://tomcat.apache.org/tomcat-7.0-doc/jasper-howto.html) 引擎將jsp轉換成java檔，然後再使用java compiler將java檔編譯成class檔。一但jsp被編譯成class檔以後，tomcat將不再使用jsp檔，而是直接使用class檔。  

假設使用者在同一個請求中存取多個jsp，其編譯所花的時間，將非常可觀。

## 預先編譯jsp  
預先編譯的方法主要有三種:  
1. **Pre-compilation by Request**  
如同上一段所述，當使用者第一次請求某jsp時執行。這是最簡單且預設的方式。
  
2. **Pre-compilation on start-up**  
另一種方式，是在**tomcat server啟動期間**編譯jsp。因此，我們必須透過修改web.xml，告訴server在啟動時編譯jsp。  
如下圖，在每個jsp頁面加上<load-on-startup>的tag即可。  
![Pre-compilation on start-up](https://i.imgur.com/sLZvngz.png)  
  
3. **Pre-compilation at build**  
這種方式是在**專案build期間**編譯jsp。build專案的工具有很多，例如[ant](http://ant.apache.org/), [maven](https://maven.apache.org/), [gradle](https://gradle.org/)等，我們使用ant作為build專案的工具。

本文採用第三種方式，Pre-compilation at build。  

## 使用ant預先編譯jsp
* 安裝ant  
> 1. [下載](http://ant.apache.org/bindownload.cgi)ant進行安裝
> 2. 設定環境變數%ANT_HOME%，%ANT_HOME%加到PATH路徑  
<img src="https://i.imgur.com/MZ8wKdc.png" alt="ANT_HOME" width="45%" height="45%"></img> <img src="https://i.imgur.com/HYYA5BN.png" alt="PATH_HOME" width="45%" height="45%"></img>
> 3. 開啟命令提示字元，輸入ant -v指令，若出現版本資訊表示安裝成功。  
<img src="https://i.imgur.com/kjimDVa.png" alt="ANT_HOME" width="45%" height="45%"></img>
* 使用ant編譯jsp  
使用ant之前需要寫一份xml，告訴ant該執行那些工作(target)。若沒有明確指定，ant預設使用[build.xml](https://github.com/hsush2017/precompile_jsp/blob/master/build.xml)進行。 

![property](https://i.imgur.com/DZGtaqs.png)  
參數設定，請依照電腦的設定更換。

![path設定](https://i.imgur.com/9nAJsFR.png)  
引入該專案會用到的jar和classpath。  

![jspc](https://i.imgur.com/rfarglb.png)  
透過JspC套件，**將jsp轉換成JAVA檔**。  
在\<taskdef\>中，定義JspC，設定JspC會用到的jar和classpath，並取名為jasper2。  
在\<jasper2\>中，轉換jsp至java。**addwebxmlmappings="true"表示在轉換時會將generated_web.xml合併到web.xml中**。傳換完成將JAVA檔存於jsp_java目錄下。



* 使用ant打包和部屬專案
* 使用ant啟動tomcat server
