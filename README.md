# Python으로 HTML Webスクレイピング하기

[![Bright Data Promo](https://github.com/bright-kr/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.co.kr/)

이 튜토리얼에서는 HTML의 기본과 Python을 사용하여 웹 데이터를 수집하고, 파싱하고, 처리하는 방법을 다룹니다:

- [HTML 구조 소개](#introduction-to-html-structure)
- [Python 환경 설정](#setting-up-your-python-environment)
- [웹페이지에서 전체 HTML 추출](#extracting-complete-html-from-a-webpage)
- [특정 HTML 요소 타겟팅](#targeting-specific-html-elements)
- [페이지 요소와 상호작용](#interacting-with-page-elements)
- [추출한 데이터를 CSV로 저장](#saving-extracted-data-to-csv)
- [마무리 생각](#final-thoughts)

포괄적인 Python web scraping 리소스를 원하시나요? [자세한 가이드를 확인해 보십시오](https://brightdata.co.kr/blog/how-tos/web-scraping-with-python).

## Introduction to HTML Structure

スクレイピング에 들어가기 전에, HTML의 구성 요소를 이해하는 것이 중요합니다.

HTML은 웹페이지의 구조와 구성 요소를 정의하는 태그로 이루어져 있습니다. 예를 들어, `<h1> Text </h1>`는 제목 텍스트를 정의하고, `<a href=""> link </a>`는 하이퍼링크를 생성합니다.

HTML 속성은 요소에 대한 추가 정보를 제공합니다. 예를 들어, `<a> </a>` 태그의 `href` 속성은 링크 목적지 URL을 지정합니다.

[Classes and IDs](https://www.scaler.com/topics/html/class-and-id-in-html/)는 페이지 요소를 정확하게 식별하는 데 중요한 속성입니다. class는 유사한 요소를 그룹화하여 CSS로 일관되게 스타일링하거나 JavaScript로 조작할 수 있게 합니다. class는 `.class-name`으로 참조합니다.

W3Schools에서 class 그룹은 다음과 같이 표시됩니다:

```html
<div class="city">
  <h2>London</h2>
  <p>London is the capital of England.</p>
</div>

<div class="city">
  <h2>Paris</h2>
  <p>Paris is the capital of France.</p>
</div>

<div class="city">
  <h2>Tokyo</h2>
  <p>Tokyo is the capital of Japan.</p>
</div>

```

각 제목과 도시 섹션이 동일한 `city` class를 공유하는 `div` 안에 포함되어 있는 점에 주목하십시오.

반면, ID는 개별 요소에 대해 고유해야 합니다(두 요소가 동일한 ID를 공유할 수 없습니다). 예를 들어, 다음 H1 요소는 개별 스타일링/조작을 위해 고유한 ID를 가집니다:

```html
<h1 id="header1">Hello World!</h1>

<h1 id="header2">Lorem Ipsum Dolor</h1>

```

ID 요소를 타겟팅하는 구문은 `#id-name`입니다.

## Setting Up Your Python Environment

이 가이드는 다양한 HTML スクレイピング 라이브러리와 초보자 친화적인 문법을 갖춘 Python을 사용합니다. Python이 설치되어 있는지 확인하려면 PowerShell(Windows) 또는 Terminal(macOS)에서 다음 명령을 실행하십시오:

```sh
python3
```

Python이 설치되어 있다면 버전 번호가 표시되며, 그렇지 않다면 오류 메시지가 표시됩니다. 필요하다면 [Python을 다운로드하여 설치하십시오](https://www.python.org/downloads/).

다음으로 `WebScraper`라는 폴더를 만들고, 그 안에 `scraper.py`라는 파일을 생성하십시오. 선호하는 통합 개발 환경(IDE)에서 이 파일을 여십시오. 이 가이드에서는 [Visual Studio Code](https://code.visualstudio.com/)를 사용합니다:

![VSCode showing the project](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/an-image-showing-where-VSC-is-used-1.png)

IDE는 개발자가 코드를 작성하고, 디버그하고, 프로그램을 테스트하고, 자동화를 만들 수 있도록 하는 종합 도구입니다. 이를 사용하여 HTML 스크레이퍼를 개발합니다.

이제 가상 환경을 생성하여 전역 Python 설치를 スクレイピング 프로젝트와 분리하십시오. 이는 의존성 충돌을 방지하고 프로젝트를 체계적으로 유지하는 데 도움이 됩니다.

다음 명령으로 `virtualenv` 라이브러리를 설치하십시오:

```sh
pip3 install virtualenv
```

프로젝트 폴더로 이동하십시오:

```sh
cd WebScraper
```

가상 환경을 생성하십시오:

```sh
python<version> -m venv <virtual-environment-name>
```

그러면 프로젝트 폴더 내에 모든 패키지와 스크립트를 위한 디렉터리가 생성됩니다:

![Virtual environment folder creation](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/Command-to-create-a-folder-for-all-the-packages-and-scripts-1.png)

이제 시스템에 맞는 적절한 명령을 사용하여 가상 환경을 활성화하십시오:

```sh
source <virtual-environment-name>/bin/activate #In MacOS and Linux

<virtual-environment-name>/Scripts/activate.bat #In CMD

<virtual-environment-name>/Scripts/Activate.ps1 #In Powershell

```

성공적으로 활성화되면 터미널 왼쪽에 가상 환경 이름이 표시됩니다:

![Virtual environment activation indicator](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/The-name-of-your-virtual-environment-1.png)

가상 환경이 활성화된 상태에서 web scraping 라이브러리를 설치하십시오. 선택지는 [Playwright](https://playwright.dev/), [Selenium](https://www.selenium.dev/), [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/), [Scrapy](https://scrapy.org/) 등이 있습니다. 이 튜토리얼에서는 사용이 쉽고, 여러 브라우저를 지원하며, 동적 콘텐츠를 처리하고, headless 모드(GUI 없이 スクレイピング)를 제공하는 [Playwright](https://playwright.dev/python/docs/intro)를 사용합니다.

`pip install pytest-playwright`를 실행하여 Playwright를 설치한 다음, `playwright install`로 필요한 브라우저를 설치하십시오.

이제 web scraping을 시작할 준비가 되었습니다.

## Extracting Complete HTML from a Webpage

어떤 スクレイピング 프로젝트에서든 첫 단계는 타겟 웹사이트를 선택하는 것입니다. 이 튜토리얼에서는 [이 e-commerce 테스트 사이트](https://webscraper.io/test-sites/e-commerce/static)를 사용합니다.

다음으로 어떤 정보를 추출할지 식별하십시오. 먼저 페이지의 전체 HTML 콘텐츠를 캡처하겠습니다.

スクレイピング 타겟을 식별한 후 스크레이퍼 코딩을 시작하십시오. Python에서는 먼저 [필요한 Playwright 라이브러리를 import](https://playwright.dev/python/docs/library)합니다. Playwright는 두 가지 API 유형([sync와 async](https://stackoverflow.com/questions/65439214/what-are-the-differences-between-python-playwright-sync-vs-async-apis))을 제공합니다. 비동기 코드를 작성하지 않으므로 sync 라이브러리를 import합니다:

```python
from playwright.sync_api import sync_playwright
```

sync 라이브러리를 import한 후, Python 함수를 정의하십시오:

```python
def main():
    #Rest of the code will be inside this function
```

모든 web scraping 코드는 이 함수 안에 위치합니다.

일반적으로 웹사이트 정보에 접근하려면 브라우저를 열고, 탭을 만들고, 사이트에 방문합니다. 스크레이핑에서는 Playwright를 사용해 이러한 동작을 코드로 옮깁니다. [문서](https://playwright.dev/python/docs/library)에 따르면, import한 `sync_api`를 호출하여 브라우저를 실행할 수 있습니다:

```python
with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
```

`headless=False`로 설정하면 실행 중 브라우저 콘텐츠를 확인할 수 있습니다.

브라우저를 실행한 후 새 탭을 열고 타겟 URL로 이동하십시오:

```python
page = browser.new_page()
try:
  page.goto("https://webscraper.io/test-sites/e-commerce/static")
except:
  print("Error")
```

> **Note:**
> 
> 이전 브라우저 실행 코드 아래에 이 줄들을 추가하십시오. 이 모든 코드는 하나의 파일에서 main 함수 내부에 포함됩니다.

이 코드는 더 나은 오류 처리를 위해 `goto()` 함수를 [try-except 블록](https://www.w3schools.com/python/python_try_except.asp)으로 감쌉니다.

URL을 입력할 때 사이트 로딩을 기다리는 것처럼, 코드에도 대기 시간을 추가하십시오:

```python
page.wait_for_timeout(7000) #millisecond value in brackets
```

> **Note:**
> 
> 이전 코드 아래에 이 줄들을 추가하십시오.

마지막으로, 페이지에서 전체 HTML 콘텐츠를 추출하십시오:

```python
print(page.content())
```

전체 HTML 추출 코드는 다음과 같습니다:

```python
from playwright.sync_api import sync_playwright

def main():
  with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)   
        page = browser.new_page()
        try:
            page.goto("https://webscraper.io/test-sites/e-commerce/static")
        except:
            print("Error")

        page.wait_for_timeout(7000)
        print(page.content())

main()
```

Visual Studio Code에서는 추출된 HTML이 다음과 같이 표시됩니다:

![Extracted HTML in VSCode](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/The-extracted-HTML-in-VSC-1.png)

## Targeting Specific HTML Elements

전체 웹페이지를 추출하는 것도 가능하지만, 특정 정보에 집중할 때 web scraping은 진정한 가치를 발휘합니다. 이 섹션에서는 웹사이트 첫 페이지에서 노트북 제목만 추출하겠습니다:

![Laptop titles to extract](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/Showing-the-titles-we-are-going-to-extract-from-the-target-website-1.png)

특정 요소를 추출하려면 먼저 웹사이트 구조를 이해해야 합니다. 페이지에서 마우스 오른쪽 버튼을 클릭하고 **Inspect**를 선택하십시오:

![Using inspect on the target website](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/Click-on-inspect-on-the-target-website-1.png)

또는 다음 키보드 단축키를 사용할 수 있습니다:
- macOS: **Cmd + Option + I**
- Windows: **Control + Shift + C**

다음은 타겟 페이지의 구조입니다:

![HTML structure of target website](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/The-HTML-structure-of-the-target-website-1.png)

**Inspect** 창의 왼쪽 상단에 있는 선택 도구를 사용하면 특정 페이지 요소를 검사할 수 있습니다:

![Inspecting specific elements](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/How-to-inspect-specific-items-in-the-source-code-1.png)

**Inspect** 창에서 노트북 제목 중 하나를 선택하십시오:

![Inspecting a laptop title](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/Inspecting-one-of-the-titles-we-want-to-scrape-1.png)

각 제목이 `<a> </a>` 태그 안에 포함되어 있고, `h4` 태그로 감싸져 있으며, 링크에 `title` class가 있는 것을 확인할 수 있습니다. 따라서 `title` class를 가진 `<h4>` 태그 내부의 `<a href>` 태그를 찾아야 합니다.

타겟팅된 スクレイピング 프로그램을 만들기 위해 필요한 라이브러리를 import하고, Python 함수를 생성하고, 브라우저를 실행한 다음, 노트북 페이지로 이동하십시오:

```python
from playwright.sync_api import sync_playwright

def main():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        page = browser.new_page()
        try:
            page.goto("https://webscraper.io/test-sites/e-commerce/static/computers/laptops")

        except:
            print("Error")

        page.wait_for_timeout(7000)
```

`page.goto()` 함수의 URL이 노트북 목록 페이지를 직접 가리키도록 업데이트된 점에 유의하십시오.

이제 구조 분석을 바탕으로 타겟 요소를 찾으십시오. Playwright는 다양한 속성을 사용해 요소를 찾는 [locators](https://playwright.dev/python/docs/locators)를 제공합니다:

- **`get_by_label()`**은 연결된 label로 요소를 찾습니다
- **`get_by_text()`**는 특정 텍스트를 포함하는 요소를 찾습니다
- **`get_by_alt_text()`**는 alt 텍스트로 이미지를 찾습니다
- **`get_by_test_id()`**는 test ID로 요소를 찾습니다

더 많은 요소 위치 지정 방법은 [공식 문서](https://playwright.dev/python/docs/locators)를 참조하십시오.

모든 노트북 제목을 추출하려면 이를 포함하는 `<h4>` 태그를 찾아야 합니다. `get_by_role()` locator를 사용하면 기능(버튼, 체크박스, headings 등) 기준으로 요소를 찾을 수 있습니다:

```python
titles = page.get_by_role("heading").all()
```

콘솔에 결과를 출력하십시오:

```python
print(titles)
```

출력은 요소 배열로 표시됩니다:

![Array of heading elements](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/Array-of-elements-after-printing-the-titles-1.png)

이 출력은 제목을 직접 보여주지 않고, 기준에 일치하는 요소를 참조합니다. 이 요소들을 루프 처리하여 `title` class를 가진 `<a>` 태그를 찾아 텍스트를 추출해야 합니다.

경로와 class로 요소를 찾기 위해 [CSS locator](https://playwright.dev/python/docs/locators#locate-by-css-or-xpath)를 사용하고, `all_inner_texts()` 함수로 텍스트를 추출하십시오:

```python
for title in titles:
  laptop = title.locator("a.title").all_inner_texts()
```

이 코드를 실행하면 다음과 같은 출력이 생성됩니다:

![Output of title extraction](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/An-image-of-how-the-output-should-look-like-1.png)

빈 배열을 필터링하려면 다음을 추가하십시오:

```python
if len(laptop) == 1:
  print(laptop[0])
```

다음은 특정 요소 스크레이퍼의 전체 코드입니다:

```python
from playwright.sync_api import sync_playwright

def main():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        page = browser.new_page()
        try:
            page.goto("https://webscraper.io/test-sites/e-commerce/static/computers/laptops")

        except:
            print("Error")

        page.wait_for_timeout(7000)

        titles = page.get_by_role("heading").all()

        for title in titles:
            laptop = title.locator("a.title").all_inner_texts()
            if len(laptop) == 1:
                print(laptop[0])

main()
```

## Interacting with Page Elements

여러 페이지에서 제목을 추출하도록 스크레이퍼를 개선해 보겠습니다. 첫 번째 노트북 페이지에서 제목을 スクレイピング하고, 두 번째 페이지로 이동한 뒤, 그 제목도 추출하겠습니다.

이미 제목을 추출하는 방법은 알고 있으므로, 다음 페이지로 이동하는 방법만 배우면 됩니다.

웹사이트 하단에는 페이지네이션 버튼이 있습니다. 프로그래밍 방식으로 "2" 버튼을 찾아 클릭해야 합니다. 페이지를 검사해 보면 이 요소는 텍스트 "2"를 가진 list item(``<li>`` 태그)임이 드러납니다:

![Pagination element with text "2"](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/The-required-element-has-an-inner-text-of-2-1.png)

`get_by_role()` selector로 list item을 찾고, `get_by_text()` selector로 "2"를 포함하는 텍스트를 찾을 수 있습니다:

```python
page.get_by_role("listitem").get_by_text("2", exact=True)
```

이는 두 조건을 모두 만족하는 요소를 찾습니다: list item이어야 하고, 텍스트가 정확히 "2"여야 합니다.

`exact=True` 파라미터는 정확한 텍스트 매칭을 보장합니다.

이 버튼을 클릭하려면 코드를 다음과 같이 수정하십시오:

```python
page.get_by_role("listitem").get_by_text("2", exact=True).click()
```

`click()` 함수는 매칭된 요소를 클릭합니다.

이제 페이지가 로드될 때까지 기다린 다음 다시 제목을 추출하십시오:

```python
page.wait_for_timeout(5000)

titles = page.get_by_role("heading").all()

for title in titles:
    laptop = title.locator("a.title").all_inner_texts()
    if len(laptop) == 1:
        print(laptop[0])
```

전체 멀티 페이지 스크레이퍼 코드는 다음과 같아야 합니다:

```python
from playwright.sync_api import sync_playwright

def main():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        page = browser.new_page()
        try:
            page.goto("https://webscraper.io/test-sites/e-commerce/static/computers/laptops")

        except:
            print("Error")

        page.wait_for_timeout(7000)

        titles = page.get_by_role("heading").all()

        for title in titles:
            laptop = title.locator("a.title").all_inner_texts()
            if len(laptop) == 1:
                print(laptop[0])
        
        page.get_by_role("listitem").get_by_text("2", exact=True).click()

        page.wait_for_timeout(5000)

        titles = page.get_by_role("heading").all()

        for title in titles:
            laptop = title.locator("a.title").all_inner_texts()
            if len(laptop) == 1:
                print(laptop[0])

main()
```

## Saving Extracted Data to CSV

スクレイピング한 데이터는 저장하고 분석해야 유용합니다. 이제 사용자가 몇 개의 노트북 페이지를 スクレイピング할지 입력하도록 하고, 제목을 추출해 CSV 파일로 저장하는 고급 프로그램을 작성하겠습니다.

먼저 CSV 라이브러리를 import하십시오:

```python
import csv
```

다음으로 사용자 입력에 따라 여러 페이지를 방문하는 방법을 결정하십시오.

웹사이트 URL 구조를 보면, 각 노트북 페이지는 URL 파라미터를 사용합니다. 예를 들어, 두 번째 페이지 URL은 https://webscraper.io/test-sites/e-commerce/static/computers/laptops?page=2 입니다.

`?page=2` 파라미터를 변경하여 다른 페이지로 이동할 수 있습니다. 사용자에게 몇 페이지를 スクレイピング할지 물어보십시오:

```python
pages = int(input("enter the number of pages to scrape: "))
```

1부터 사용자가 지정한 숫자까지 각 페이지를 방문하려면 `for` 루프를 사용하십시오:

```python
for i in range(1, pages+1):
```

`range` 함수는 첫 값(1)에서 시작하여 두 번째 값(pages+1) 직전까지 끝납니다. 예를 들어, `range(1,5)`는 1부터 4까지 루프를 돕니다.

이제 루프 변수 `i`를 URL 파라미터로 사용하여 각 페이지를 방문하십시오. [Python f-string](https://www.geeksforgeeks.org/formatted-string-literals-f-strings-python/)을 사용하면 문자열에 변수를 삽입할 수 있습니다.

f-string은 따옴표 앞에 `f` 접두사를 붙이며, 중괄호로 변수를 삽입할 수 있습니다:

```python
print(f"The value of the variable is {variable_name_goes_here}")
```

우리 스크레이퍼에서는 내비게이션 코드에 f-string을 사용하십시오:

```python
try:
    page.goto(f"https://webscraper.io/test-sites/e-commerce/static/computers/laptops?page={i}")
except:
    print("Error")
```

페이지 로딩을 기다린 다음 제목을 추출하십시오:

```python
page.wait_for_timeout(7000)
titles = page.get_by_role("heading").all()
```

다음으로 CSV 파일을 열고, 각 제목을 루프 처리하여 텍스트를 추출한 뒤 파일에 기록하십시오.

다음과 같이 CSV 파일을 여십시오:

```python
with open("laptops.csv", "a") as csvfile:
```

`laptops.csv`를 append 모드(`a`)로 열고 있습니다. append를 사용하면 기존 데이터를 지우지 않고 새 데이터가 추가됩니다. 파일이 없으면 생성됩니다. [CSV는 여러 파일 모드를 지원합니다](https://www.learnbyexample.org/reading-and-writing-csv-files-in-python/):

- **r**: 기본 모드, 읽기 전용으로 파일을 엽니다
- **w**: 쓰기 모드로 파일을 열며 기존 데이터를 덮어씁니다
- **a**: append 모드로 파일을 열며 기존 데이터를 보존합니다
- **r+**: 읽기 및 쓰기 모두를 위해 파일을 엽니다
- **x**: 새 파일을 생성합니다

다음으로 CSV 파일을 조작하기 위해 [writer object](https://docs.python.org/3/library/csv.html#csv.writer)를 생성하십시오:

```python
writer = csv.writer(csvfile)
```

각 title 요소를 루프 처리하고 텍스트를 추출하십시오:

```python
for title in titles:
    laptop = title.locator("a.title").all_inner_texts()
```

빈 배열을 필터링하고 유효한 제목을 CSV에 기록하려면:

```python
if len(laptop) == 1:
    writer.writerow([laptop[0]])
```

`writerow` 함수는 CSV 파일에 새 행을 추가합니다.

다음은 CSV 내보내기 스크레이퍼의 전체 코드입니다:

```python
from playwright.sync_api import sync_playwright
import csv

def main():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        page = browser.new_page()
        pages = int(input("enter the number of pages to scrape: "))

        for i in range(1, pages+1):
            try:
                page.goto(f"https://webscraper.io/test-sites/e-commerce/static/computers/laptops?page={i}")

            except:
                print("Error")

            page.wait_for_timeout(7000)

            titles = page.get_by_role("heading").all()

            with open("laptops.csv", "a") as csvfile:
                writer = csv.writer(csvfile)
                for title in titles:
                    laptop = title.locator("a.title").all_inner_texts()
                    if len(laptop) == 1:
                        writer.writerow([laptop[0]])

        browser.close()             


main()
```

이 코드를 실행한 후 CSV 파일은 다음과 같이 표시되어야 합니다:

![CSV file output example](https://github.com/bright-kr/html-scraping-with-python/blob/main/images/Example-of-how-the-CSV-file-should-look-like-1.png)

## Final Thoughts

이 가이드는 기본적인 web scraping을 보여주지만, [현실 세계의 시나리오](https://brightdata.co.kr/use-cases)에서는 CAPTCHA, レート制限, 사이트 레이아웃 변경, 규제 요구사항과 같은 과제가 자주 발생합니다. Bright Data는 スクレイピング 성능을 개선하기 위한 [고급 レジデンシャルプロキシ](https://brightdata.co.kr/proxy-types/residential-proxies), 확장 가능한 스크레이퍼 구축을 위한 Web Scraper IDE, 차단된 사이트에 접근하기 위한 [Web Unblocker](https://brightdata.co.kr/products/web-unlocker) 등 이러한 과제를 해결하기 위한 [솔루션](https://brightdata.co.kr/products)을 제공합니다.

지금 무료 체험을 시작해 보십시오!