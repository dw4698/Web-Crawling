# web-crawling


## 해외직구 정보를 얻기 위한 데이터 시각화



#### 해외직구정보에 관한 웹사이트를 크롤링 하려는 이유


해외직구란 직접구매의 준말이다
인터넷 쇼핑이 활성화되고 해외 결제가 가능해지면서 범위가 전 세계로 확장되었다
구매비와 택배비 등을 포함해도 해외에서 물건을 구매하는 것이 가성비가 훨씬 좋은 경우가 점점 많아지고 있다
특히 정식 수입 제품은 국내에 들어오기만 하면 유독 비싸지는 경향이 있어 합리적인 소비를 원하는 사람들이 해외 직구를 선호하게 되었다

따라서 얼마나 빠르게, 많이 해외 사이트의 할인 정보를 알고 있냐가 중요한 해외직구 쇼핑에서
이에 대한 정보들을 알려주는 웹 페이지를 크롤링을 하는 것은 실질적인 쇼핑에 도움이 될 것이라고 생각해서 정하게 되었다

![캡처](https://user-images.githubusercontent.com/57972891/70865506-c85daf00-1fa1-11ea-83c2-200ac9c2d328.PNG)

#### 이용하고자 하는 패키지


* rvest, RSelenium, tidyverse등을 설치한다.

* docker를 설치하여 RSelenium을 이용하려고 했으나 윈도우 버전이 맞지 않아 설치가 안 되어 자바를 이용한다.

* chromedriver, geckodriver, selenium-server-standalone-3.9.1을 다운받아 cmd에 java-Dwebdriver.gecko.driver="geckodriver.exe" -jar selenium-server-standalone-3.9.1.jar -port 4446를 입력한 뒤 서버를 연결해준다


![캡처2](https://user-images.githubusercontent.com/57972891/70866009-7c156d80-1fa7-11ea-939d-eb749ffccfb4.PNG)

#### 웹 페이지 분석

* 해당 사이트 '어미새'는 빠른 소식들이 올라오기 때문에 자주 갱신된다

* 어떤 해외사이트에서 어떤 프로모션과 이벤트를 진행하고 있는지, 쿠폰이 발급 되었는지, 인기있는 브랜드의 제품이 재발매 되었는지 등등
해외직구에 대한 다양한 정보들을 알려주는 '어미새'라는 사이트를 분석하기로 했다.

* 이 사이트에서 볼 만한 게시판은 '인기 정보'와 '패션 정보'이다. 인기 정보는 패션 정보 게시판에서 구매 인증글이 많은 게시물이 인기정보 게시판으로 자동 이동된다.

* 주로 인기 많은 브랜드의 소식들은 빠르게 많은 댓글이 달리고 해당 제품은 빠르게 품절된다는 특징이 있다.
따라서 조회,댓글의 수에 비례해서 브랜드의 인기를 예측할 수 있다

> 인기정보, 패션정보 게시판
![캡처1](https://user-images.githubusercontent.com/57972891/70865958-b7fc0300-1fa6-11ea-8313-1f67c7602662.PNG)

> 조회, 댓글 수
![캡처4](https://user-images.githubusercontent.com/57972891/70866101-b29fb800-1fa8-11ea-8047-f286802f6759.PNG)

> 댓글 반응
![캡처5](https://user-images.githubusercontent.com/57972891/70866115-ec70be80-1fa8-11ea-8f2f-05da7d6aaf85.PNG)


> 나는 코스(cos)라는 브랜드의 소식을 기다리고 있기 때문에 해당 브랜드의 소식을 유심하게 크롤링 한다. 

library(rvest)
library(RSelenium)

remDr <- remoteDriver(
  remoteServerAddr = "localhost",
  port = 4446L,
  browserName = "chrome"
)

remDr$open()

remDr$navigate("http://www.google.com")
webElem <- remDr$findElement(using = "css", "[name = 'q']")
webElem$sendKeysToElement(list("R Cran", key = "enter"))

webElems <- remDr$findElements(using = "css selector", "h3.r")
resHeaders <- unlist(lapply(webElems, function(x) {x$getElementText()}))
resHeaders

webElem <- webElems[[which(resHeaders == "R for Windows")]]
webElem$clickElement()
remDr$getCurrentUrl()

remDr$close() 
library(tidyverse)
install.packages("tidyverse")

driver <- rsDriver(browser=c("chrome"))
remote_driver <- driver[["client"]]
remote_driver$open()

remote_driver$navigate("https://eomisae.co.kr/os")

driver <- rsDriver(browser = c("chrome"), chromever = "78.0.3904.70")
remote_driver <- driver[["client"]] 
remote_driver$navigate("https://eomisae.co.kr/os")

address_element <- remote_driver$findElement(using = 'class', value = 'width70')

address_element$sendKeysToElement(list("cos"))


url <- "https://eomisae.co.kr/os"
remote_driver$navigate(url)



