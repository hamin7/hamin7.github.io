---
title: Pandas와 Matplotlib로  데이터 시각화 하기
author: Hamin
date: 2020-09-24 20:45:22
tags: [Python, Pandas, Matplotlib, Crawling]
categories:
- [Python, Pandas]
- [Python, Matplotlib]
- [Crawling]
thumbnail: /gallery/matplotlib.png
---

Matplotlib은 파이썬에서 데이터를 다양한 형태로 시각화해주는 라이브러리 입니다.

Pandas를 이용하여 데이터를 수집하고 분석해 보겠습니다.

<!--more-->

COVID-19 확진자 수와 기타 데이터를 bar 그래프로 그려주는 코드를 사용해보겠습니다.

~~~python
import pandas as pd
import urllib.request as urllib
import matplotlib.pyplot as plt

url = r'https://www.worldometers.info/coronavirus/'

# urllib.request로 오프너 생성
opener = urllib.build_opener()

# User-Agent 값을 요청 헤더에 포함
opener.addheaders = [('User-Agent', 'Mozilla/5.0')]

# 크롤링 수행
response = opener.open(url)

# 크롤링한 첫 번째 테이블을 table 변수에 저장
table = pd.read_html(response.read())[0]

# Top 20개 국가만 출력하기 위해 head값을 21로 지정
table = table.head(21)

# 특정 칼럼값만을 조회하기 위해 데이터 프레임 설정
df = pd.DataFrame(table, columns=['Country,Other', 'TotalCases', 'TotalDeaths', 'ActiveCases', 'TotalRecovered', 'Serious,Critical'])

# 첫 번쨰와 두 번째 래코드값은 값이 너무 커 그래프를 왜곡하므로, 여기에서 버려야 함.
df = df.drop(8)
df = df.drop(1)

# 인덱스는 국가로 설정한다. 인덱스는 그래프에서 x축 값으로 사용.
df = df.set_index('Country,Other')

# 데이터 프레임을 출력해서 확인하자
print(df)

# 격자를 넣고, X/Y 축의 칼럼명, 그래프 이름을 설정
bar = df.plot.bar(grid = True)
bar.set_xlabel("Country")
bar.set_ylabel("Number")
bar.set_title("COVID-19 Information per Country")

# 준비되었다면 그래프를 그려보자
plt.show()
~~~

실행 결과는 다음과 같습니다.

{% asset_img result.jpg %}

"Country, Other"를 인덱스로 설정했기 때문에 기본 인덱스인 숫자 0,1,2... 대신 국가 이름을 사용합니다.

{% asset_img graph.jpg %}

그래프는 위와 같이 나옵니다.
격자가 추가된 그래프에는 그래프, x축, y축 이름이 각각 설정되었고, 데이터 프레임에 저장된 칼럼 중에 국가 정보는 인덱스로 설정되어 x축으로, 기타 값들은 y축으로 설정되어 막대그래프로 표현되었습니다.
막대그래프에 커서를 대면 숫자를 읽을 수 있습니다.

이 코드는 {% link 이 포스트 https://matplotlib.org/ [external] [title] %}를 참고하여 작성하였습니다.

<!--https://blog.naver.com/PostView.nhn?blogId=sehyunfa&logNo=221908206514&categoryNo=60&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView-->