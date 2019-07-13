# Django架設網站及上傳至Heroku
本教學不教怎麼架Django，而是教怎麼去改Django，以及更新到Heroku上
* **<h3>第一步驟:註冊Heroku</h3>**  
  到官網上下載[Heroku](https://devcenter.heroku.com/articles/getting-started-with-python#set-up)

* **<h3>第二部分為:開始上傳至heroku</h3>**  
  * 請將django_web_file.zip這個壓縮檔解壓縮，包含虛擬環境，安裝包都在裡面
  * 打開命令提示字元
    **1.cd到django_web_file，並進入虛擬環境**  
    > D:\桌面用\蘭陽博物館app\django_web_file>djangogirls_venv\Scripts\activate
    
    ![image](https://github.com/PoChuanHuang/LineBot-MuseumBot/blob/master/Realtime%20Bus%20Info/%E9%80%B2%E8%99%9B%E6%93%AC%E7%92%B0%E5%A2%83.png)
    
    **2登入heroku**  
    會連結到網頁進行登入，登入好就可以關掉網頁了
    > Heroku login
    
      
    **3.建立一個heroku web(或叫app)**  
    > heroku create yanyangmumtest
    
    ![image](https://github.com/PoChuanHuang/LineBot-MuseumBot/blob/master/Realtime%20Bus%20Info/heroku%20create%20yanyangmumtest.png)
    
    名字限定為小寫、數字、"-" (不能有”_”)
    成功後會產生兩樣東西
    一樣是你的專屬網址 ->         https://yanyangmumtest.herokuapp.com
    一樣是git的連結 -> https://git.heroku.com/yanyangmumtest.git
    > heroku apps
    
    可以用這個檢視是否成功產生
    在輸入
    > heroku git:remote -a yanyangmumtest
    
    ![image](https://github.com/PoChuanHuang/LineBot-MuseumBot/blob/master/Realtime%20Bus%20Info/heroku%20git%20remote.png)
    
    表示我設定heroku連結到yanyangmumtest這個web/app
    其中是利用git remote到               https://git.heroku.com/yanyangmumtest.git

    **4.確保未來在 Heroku 執行任何指令時，都是使用到部署專用的設定檔**  
    > heroku config:set DJANGO_SETTINGS_MODULE=Yilanmumsite.production_settings=
    
    **7.上傳至 Heroku 伺服器**  
    > git push heroku master 
    
    注意:這裡push的動作是將django文件上傳至heroku，若檔案有什麼問題或是設定沒設定好，常會在這裡push failed
    
      
    **8.啟動/關閉 伺服器**  
    此刻要在heroku上啟動webserver
    > heroku ps:scale web=1
    
    若之後要關閉則是如下指令
    >heroku ps:scale web=0
    
    同時亦可透過->heroku ps來觀看目前webserver狀態
 
      
    **9.初始化資料庫**   
    > heroku run python Yilanmumsite/manage.py migrate
    
    ![image](https://github.com/PoChuanHuang/LineBot-MuseumBot/blob/master/Realtime%20Bus%20Info/%E5%88%9D%E5%A7%8B%E5%8C%96.png)
      
    **10.為後台建立一個使用者**
    >heroku run python Yilanmumsite/manage.py createsuperuser
    
    最後就可以用
    >heroku open
    
    打開你的網址囉
    之後有任何在django上的變動
    都可透過git指令上傳版本更新
    >git status 
    
    可看哪些檔案更新了(可能是新增刪除修改等等)
    >git add .
    >git commit –m “add”
    >git push heroku master
    
    即可更新完成
    
    **如有發生錯誤，請善用這個指令來看logs，觀看網站執行的紀錄**
    >heroku logs --tail


  
* **<h3>第三部分為Django使用教學</h3>**    
首先點開django_web_file/Yilanmumsite檔案目錄
![image](https://github.com/PoChuanHuang/LineBot-MuseumBot/blob/master/Realtime%20Bus%20Info/%E6%AA%94%E6%A1%88%E7%9B%AE%E9%8C%84.png)
接著點進Yilanmumsite，查看urls.py
```gherkin=
urlpatterns = [
    url(r'^admin/', include(admin.site.urls)),
    url(r'^to131/$',to131),
    url(r'^back131/$',back131),
    url(r'^to1766/$',to1766),
    url(r'^back1766/$',back1766),
    url(r'^tor1/$',tor1),
    url(r'^backr1/$',backr1),
    url(r'^to1877/$',to1877),
    url(r'^back1877/$',back1877),
    url(r'^$', home),
]
```
其中這段就是在設定url
例如:
url(r'^back1877/$',back1877),
就是說在https://yanyangmumtest.herokuapp.com/後面再加上back1877
變成https://yanyangmumtest.herokuapp.com/back1877/
就會執行back1877()這個函式
back1877()在trips檔案目錄裡的view.py
在view.py裡就會看到爬蟲程式
以及很重要的
```gherkin=
return render(request, 'to131.html', {
        'bus_step1':msg[0],'bus_step2':msg[1],'bus_step3':msg[2],'bus_step4':msg[3],
        'bus_step5':msg[4],'bus_step6':msg[5],'bus_step7':msg[6],'bus_step8':msg[7],
        'bus_step9':msg[8],'bus_step10':msg[9],'bus_step11':msg[10],'bus_step12':msg[11],
        'bus_step13':msg[12],'bus_step14':msg[13],'bus_step15':msg[14],'bus_step16':msg[15],
        'bus_step17':msg[16],'bus_step18':msg[17],'bus_step19':msg[18],
        'current_time': str(datetime.now()),
        })
```
剛剛執行了urls這個函式，並且傳送back1877這個html_request
傳送到view.py的back1877()這個函式
並且進行爬蟲後，
接著要用render把request、html、各種data，response回去
其中所有html檔都在templates檔案目錄裡
其他python檔都沒用到
