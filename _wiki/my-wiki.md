---
layout  : wiki
title   : Vimwiki + Jekyll + Github.io로 나만의 위키를 만들자
summary : 마음에 드는 무료 위키가 없어서 만들어보았다
date    : 2017-12-06 21:44:18 +0900
updated : 2021-05-22 20:53:37 +0900
tag     : wiki vimwiki jekyll blog
toc     : true
comment : true
public  : true
parent  : [[blog]]
---
* TOC
{:toc}

## Vimwiki

[Vimwiki](https://github.com/vimwiki/vimwiki)는 Vim 사용자를 위한 위키 플러그인이다.  
기능이 꽤 많지만 다 알 필요는 없다.  
엔터 키 하나만 알아도 자신만의 위키를 구축할 수 있기 때문이다.

만약 내가 `test`라는 단어에 커서를 놓고 엔터를 입력하면,

* `test.md` 파일을 열어준다. 파일이 없다면 새로 만들고 열어준다.
* 링크를 생성한 `test`에는 대괄호 두 개가 `\[[test]]` 자동으로 입혀진다.
* 좀 전에 보던 파일로 돌아가려면 백스페이스 키를 누르면 된다.

즉, 어떤 단어에 대해 기록하고 싶은 것이 있다면 그냥 엔터를 누르고 원하는 내용을 기록하면 된다.  
간단하게 로컬 위키를 만들고 관리할 수 있게 되는 것이다.  
생성되는 파일은 단순한 텍스트 파일이므로 git과 github을 사용해 관리하면 반영구적으로 집과 회사에서 사용할 수 있다.

게다가 공짜다.

## Vimwiki + Jekyll + Github.io

그런데 Vimwiki를 쓰다 보니, Jekyll과 Github.io를 끼얹어 적절히 마개조하면 개인용 위키 웹을 만들 수 있을 거라는 생각이 들었다.

일단 다음과 같이 간단한 요구사항을 생각해 보았다.

### 쉽고 빠르게 새로운 문서를 추가할 수 있어야 한다

제일 중요한 기능이다. 귀찮으면 새 문서를 안 쓸 테니까.

* Vimwiki가 있으니 엔터 키 하나로 새로운 항목을 추가하고 링크도 연결할 수 있다. 이보다 편하긴 어렵다.
* 로컬에서 예상치 못한 귀찮은 문제가 생기면 Vimscript로 해결하면 된다.
* 웹에서 링크 연결하는 문제는 Javascript를 써서 해결하자.
    * `\[[test]]`를 `<a href="../test.md">test</a>`로 리플레이스하면 될 것 같다.

### 검색 기능이 있어야 한다

검색 없는 위키는 본 적이 없다. 모두 망했기 때문일 것이다.

* 터미널에서: Vimwiki는 로컬 검색 기능이 있다. 터미널에서 `ag`를 써도 된다.
* 웹에서: Google Custom Search(구글 맞춤 검색)를 붙이면 된다.

### history 기능이 있어야 한다

* 언제 어느 부분이 누구에 의해 어떻게 편집되었는지를 공개하는 것은 wiki의 기본.
* github의 blame 페이지를 링크해 보여주면 될 것 같다.
* updated date는 파일 변경일자를 보여주자.

### 최근 변경된 문서 목록이 나오면 그럴싸할 것 같다

* 파일 변경일자별로 역순 정렬을 해서 보여주면 된다.
* Jekyll의 liquid 문법이 좀 별로이긴 하지만 설마 역순 정렬이 안 될 것 같지는 않다.

### 각 문서마다 상위 문서 목록이 나왔으면 좋겠다

* 문서마다 `parent` 프로퍼티를 지정하면 될 것 같다.
* 한 레벨 위 부모만 보여주는 게 아니라 `동물 - 포유류 - 강아지` 처럼 root까지 나오면 좋겠다.
* liquid는 미개해서 `while` 문이 없다. 10 번 정도 돌리면서 부모를 찾아가다 부모 문서가 없으면 멈추면 될듯.
* 부모 자식 문서 연결은 10레벨 정도면 충분할 것 같다. `종-속-과-목-강-문-계`도 7 레벨이니까.

### 태그 기능이 있으면 좋겠다

* 위키 페이지에서 관련 문서를 쉽게 찾아볼 수 있게 된다.
* Github.io의 Jekyll 기본 플러그인인 `jekyll-tag`를 이용할 수 있을 것 같다.
* 만약 `jekyll-tag`의 기능이 썩 마음에 안 들면 데이터 파일을 생성하는 스크립트를 짜면 된다.

### 타인이 기여할 수 있으면 좋겠다

아무도 안 하겠지만.

* Github 기반이니까 누구나 pull request 날릴 수 있다.
* 받아주고 말고를 내가 결정할 수 있는 것도 좋다.

## 구현

하나씩 구현해 보았다.

### Vimwiki 설정

Vimwiki부터 설정해야 한다.

Vimwiki 플러그인을 설치한 다음, `.vimrc`에 다음 설정 코드를 추가해 준다.

```viml
let wiki = {}
let wiki.path = '~/johngrib.github.io/_wiki/'
let wiki.ext = '.md'

let g:vimwiki_list = [wiki]
let g:vimwiki_conceallevel = 0
```

* `wiki.syntax`를 `markdown`를 설정하지 않고 기본 값으로 둔 이유.
    * 엔터 키로 생성하는 링크가 `\[[ ]]`가 아니라 `[  ]()`의 형태로 생성된다. 이렇게 되면 Vimwiki만 쓰기엔 좋은데 Jekyll과 함께 쓰기가 짜증난다. Vimwiki는 링크 위치를 설정 값을 보고 찾아가지만, Jekyll은 그러지 못한다.

* `vimwiki_conceallevel`을 `0`으로 설정한 이유.
    * Vimwiki는 기본적으로 사용자에게 몇몇 특수문자를 보여주지 않도록 설정되어 있다.
    * 문제는 Vim에 익숙한 사용자일수록 이게 더 불편하고, 숨기는 문자가 많을수록 속도가 느려질 수 있다는 것이다.
    * 아무 것도 숨기지 않도록 `0`으로 설정하는 것이 정신건강에 좋다.

### `\[[test]]`형식의 Vimwiki 링크를 html `<a>` 태그로 보여준다.

[_includes/createLink.html](https://github.com/johngrib/johngrib-jekyll-skeleton/blob/v1.0/_includes/createLink.html) 파일을 만들고 다음과 같이 Javascript 코드를 짜 넣었다.

```javascript
<script>
    ;(function() {
        var content = document.querySelector('article.post-content');
        content.innerHTML = content.innerHTML.replace(/\[\[(.+?)\]\]\{(.+?)\}/g, '<a href="../$1">$2</a>');
        content.innerHTML = content.innerHTML.replace(/\[\[(.+?)\]\]/g, '<a href="../$1">$1</a>');
    })();
</script>
```

위의 javascript 코드는 다음과 같은 두 가지 패턴에 대해 `<a>` 태그를 생성한다.

* `\[[test]]{테스트}` => `<a href="../test">테스트</a>`
* `\[[test]]` => `<a href="../test">test</a>`

이제 `_includes/createLink.html`을 위키 레이아웃에 집어넣으면 Vimwiki의 링크 형식이 웹으로 볼 때에는 `<a>`태그 링크로 보이게 된다.

```liquid
{% raw %}
{% include createLink.html %}
{% endraw %}
```

#### 우려: 항목이 많아지면 링크 생성이 느리지 않을까?

이 방식은 포스트 내의 모든 텍스트를 긁어와 replace하는 방식이다.  
항목이 많아지면 링크 생성 때문에 웹 페이지가 뜨는 시간도 오래 걸릴 것이다.  
느낌상으로는 느려져 봤자일 것 같지만, 기왕 하는 김에 테스트를 해보기로 했다.

테스트는 3가지 기준으로 수행하였다.  

|                         | 링크가 15 개   | 링크가 1481 개   | 링크가 30000 개   |
| ------------------      | -------------: | ---------------: | ----------------: |
| 프로젝트 전체 빌드      | 1.888894s      | 2.889067s        | 14.884344s        |
| 테스트 페이지 로딩 완료 | 0.615s         | 0.827s           | 3.83s             |

* 테스트 페이지는 localhost에 띄우고 테스트하였다.
* 프로젝트 전체 빌드는 테스트용 웹 페이지 하나가 아니라 프로젝트 전체(약 50개의 파일)을 빌드한 결과이다.
* 테스트 페이지를 오픈한 브라우저는 구글 크롬 62.0.3202.94(공식 빌드) (64비트).
* before/after의 차이가 뻔해서 테스트는 한 번만 수행했다.

##### 링크 15개

일반적으로 약간 내용이 많은 문서라면 링크가 15개는 들어갈 것 같았다.
아마 대부분의 문서는 테스트 문서보다 짧은 시간 안에 로드될 것이다.

##### 링크 1481 개

[Computer_science(wikipedia)](https://en.wikipedia.org/wiki/Computer_science) 문서를 조사해 보니 링크가 1481개[^1] 있었다.  
나 혼자 작성하는 위키 페이지가 이 문서보다 많은 링크를 포함할 것 같지는 않아서 이걸로 선정했다.  
오픈 시간이 1초 미만이니 사용에 큰 불편은 없을 것 같다.

##### 링크 30000 개

극단적인 경우도 테스트해보고 싶어서 링크 30000개를 테스트해 보았다.  
링크 20000개는 `\[[test]]{테스트}`의 형식이고, 10000개는 `\[[test]]`의 형식이었다.

일단 가장 큰 차이가 나는 건 빌드 시간. 하지만 15초 정도는 얼마든지 기다릴 수 있다.  
빌드된 웹 페이지를 Jekyll 로컬 서버로 띄워 크롬에서 열어보니 4초 정도 걸린다.  
Google Analytics를 빼뒀으면 더 빨랐을 것이다.

문서 하나에 링크가 30000개 있을 때 페이지가 열리는 데 4 초 걸리는 셈이니 걱정할 필요는 없을 것 같다.  
하루에 문서 하나씩 만든다고 가정했을 때, 83년을 매일같이 작성해야 30000개의 문서를 만들 수 있기 때문이다.  
그렇게 만든 모든 문서를 한 페이지에 넣는 경우는 wiki index 하나 뿐일테니 안심이다.

### 검색 기능

[Google Custom Search(구글 맞춤 검색)](https://www.google.co.kr/cse/)를 붙이면 된다. 구글 만세!

구글 맞춤 검색 생성 코드를 얻어온 다음, 다음과 같은 파일을 작성하였다.

* [search.html](https://github.com/johngrib/johngrib-jekyll-skeleton/blob/v1.0/search.html ) - 최초 버전
* [search.html](https://github.com/johngrib/johngrib.github.io/blob/master/search.html ) - 최신 버전

```html
---
layout: searchList
title: Search
permalink: /search/
public: true
---
 <div id="home-search">
     <script>
         (function() {
             var cx = '나의 구글 맞춤 검색 코드';
             var gcse = document.createElement('script');
             gcse.type = 'text/javascript';
             gcse.async = true;
             gcse.src = 'https://cse.google.com/cse.js?cx=' + cx;
             var s = document.getElementsByTagName('script')[0];
             s.parentNode.insertBefore(gcse, s);
         })();
     </script>
     <gcse:search linkTarget="_parent" queryParameterName="searchString"></gcse:search>
 </div>
```

이렇게 하면 `johngrib.github.io/search`로 들어갔을 때 구글 맞춤 검색 페이지가 보이게 된다.

추가로 모든 페이지 최상단에 검색어 입력칸을 보여주고 싶었기 때문에 다음과 같이 `searchBox.html`이란 파일도 추가했다.  

* [_includes/searchBox.html](https://github.com/johngrib/johngrib-jekyll-skeleton/blob/v1.0/_includes/searchbox.html ) - 최초 버전
* [_includes/searchBox.html](https://github.com/johngrib/johngrib-jekyll-skeleton/blob/master/_includes/searchbox.html ) - 최신 버전

```html
<div class="search">
    <form role="search" method="get" action="/search/">
         <input name="searchString" class="searchInput" placeholder=" 검색하세요" type="text">
         <input type="submit" class="searchButton" value="Search">
    </form>
</div>
```

그리고 다음과 같이 [_layouts/document.html](https://github.com/johngrib/johngrib-jekyll-skeleton/blob/v1.0/_layouts/document.html)에 집어넣었다.

```html
{% raw %}
<!DOCTYPE html>
<html>
    {% include head.html %}
    {% include header.html %}
    <body>
        <div class="page-content">
            {% include searchbox.html %}    <!-- 여기에 집어넣었다 -->
            <div class="post">
                {{ content }}
            </div>
        </div>
        {% include footer.html %}
    </body>
</html>
{% endraw %}
```

이제 사이트맵을 만들어 구글에 등록해줘야 한다.

사이트맵은 xml 파일이지만, liquid를 사용해 jekyll이 동적으로 생성하도록 하면 된다.

내가 작성한 사이트맵 파일은 다음 링크로 들어가면 참고할 수 있다.

[sitemap.xml](https://github.com/johngrib/johngrib-jekyll-skeleton/blob/master/sitemap.xml )

그리고 [search.console.com](https://search.google.com/search-console )에 들어가서 사이트맵 uri를 입력해주면 된다.

사이트맵을 등록하고 3~4일쯤 지나면, 블로그의 글이 구글에서 검색된다.

처음 사이트맵을 등록하면 며칠이 지나도 검색이 안 되어서 내가 잘못 등록한 건가? 하는 생각을 하게 되는데
마음 편하게 일주일 정도 기다려 보길 권한다.

오늘 발행한 글이 며칠 기다려야 검색이 된다는 것은 성격 급한 내게는 꽤 답답한 일이었다.

하지만 익숙해진 지금은 신경쓰지 않는다.

잘못 쓴 내용이 있을 경우 수정할 여유가 있다는 말이기도 하고,
검색에 빨리 걸린다고 해서 안 달리는 댓글이 더 달리는 것도 아니다.


### history 기능

Github의 blame 기능을 쓰면 된다.

일단 blame 주소를 입력해야 하므로 `_config.yml` 파일에 다음 항목을 추가하였다.  
나는 wiki에 사용할 마크다운 파일을 `_wiki` 디렉토리에 보관하고 있으므로 경로가 다음과 같았다.

```yml
blame: "https://github.com/johngrib/johngrib.github.io/blame/master/_wiki"
```

그리고 [_layouts/wiki.html](https://github.com/johngrib/johngrib-jekyll-skeleton/blob/v1.0/_layouts/wiki.html#L10)에는 다음과 같이 blame 주소가 자동으로 입력되도록 하였다.

```html
{% raw %}
{% assign thisName = page.id | remove: '/wiki/' %}

<div class="post">
    <header class="post-header">
        <h1 class="page-title">{{ page.title }}</h1>
        <p class="edit-date"><a href="{{ site.blame }}/{{ thisName }}.md" target="_blank">updated: {{ page.updated | date: '%Y.%m.%d' }}</a></p>
...
{% endraw %}
```

### 최근 변경된 문서 목록

최근 변경된 문서 목록을 만들기 위해서는 문서별로 최근 변경된 시간을 구할 수 있어야 한다.

그런데 Jekyll은 파일의 마지막 변경 시간을 구할 수 없었다.

Github.io에서는 사용할 수 있는 Jekyll 플러그인이 제한되어 있어서, 내가 직접 플러그인을 만들어도 원하는 레벨로 사용할 수가 없다.

그렇다면 마크다운 상단에 프로퍼티를 적는 방식으로 해결하기로 결정.

다음과 같이 `updated` 프로퍼티를 추가하고, Jekyll이 읽어오게 하면 된다.

```markdown
---
layout  : post
title   : 타이틀명
summary : 요약
updated : 2017-12-07 13:03:30 +0900
toc     : true
comment : false
public  : true
---
```

그리고 `updated`를 파일을 편집할 때마다 직접 수정하는 건 귀찮은 일이니까, [Vim Tips Wiki](http://vim.wikia.com/wiki/Insert_current_date_or_time)를 참고하여 다음과 같은 코드를 `autocmd`로 `.vimrc`에 추가해 주었다.

```vimscript
function! LastModified()
    if &modified
        let save_cursor = getpos(".")
        let n = min([10, line("$")])
        keepjumps exe '1,' . n . 's#^\(.\{,10}updated\s*: \).*#\1' .
              \ strftime('%Y-%m-%d %H:%M:%S +0900') . '#e'
        call histdel('search', -1)
        call setpos('.', save_cursor)
    endif
endfun
autocmd BufWritePre *.md call LastModified()
```

이렇게 하면, Vim에서 마크다운 파일을 편집하다가 저장하면 자동으로 제일 위 1~10번 라인 중 `updated : ` 항목이 있으면 현재 시간을 자동으로 입력해 준다.

그리고 다음과 같은 방법을 쓰면 최근 변경된 문서 목록을 역순으로 출력할 수 있다.

[index.html](https://github.com/johngrib/johngrib-jekyll-skeleton/blob/v1.0/index.html#L37)

```liquid
{% raw %}
<div>
    <H3>최근 변경된 문서</H3>
    <ul class="post-list">
{% assign documents = site.wiki | sort: 'updated' | reverse %} <!-- 정렬 후, 뒤집는다 -->
{% for doc in documents limit: 10 %}
    {% if doc.public == true and doc.title != 'wiki index'%}
        <li>
            <div>
                <a class="post-link" href="{{ doc.url | prepend: site.baseurl }}">
                    <div class="post-meta">
                        {{ doc.updated | date: "%Y.%m.%d" }}
                        -
                        {{ doc.title }}
                    </div>
                    <div class="post-excerpt">{{ doc.summary }}</div>

                    <ul class="tag-list">
        {% for tag in doc.tags %}
                        <li class="post-tag">
                            <a href="/tag/#{{tag}}" >#{{tag}}</a>
                        </li>
        {% endfor %}
                    </ul>
                </a>
            </div>
        </li>
    {% endif %}
{% endfor %}
    </ul>
</div>
{% endraw %}
```

### 상위 문서 목록

이걸 구현하려면 각각의 페이지에 `parent` 프로퍼티를 추가해야 한다.  
링크드 리스트처럼 다음 부모를 찾아 올라가는 작업을 반복해서 부모의 리스트를 만든 다음, 역순으로 정렬해 링크를 만들어 주면 된다.  

처음엔 Github.io의 Jekyll이 기본으로 제공하는 `jekyll-tag` 플러그인을 사용하고 싶었다.  
그러나 나중에 알게 된 건데, Github.io의 Jekyll에서는 사용할 수 있는 플러그인이 따로 있었다[^2].  
ruby를 써서 내가 직접 Jekyll 플러그인을 개발해도 Github.io에서는 사용할 수 없다는 것.

이건 Github.io의 Jekyll이 제공하는 플러그인의 한계가 있는 것이므로,  
javascript로 yml 데이터 파일을 생성하는 코드를 짜기로 했다.  
Jekyll은 `_data` 디렉토리에 `json`이나 `yml`파일이 있다면 `site.data.파일명.키` 와 같은 방법으로 해당 데이터에 접근할 수 있다.

이걸 이용하면 된다.

내가 작성한 yml 데이터 파일 생성기 코드는 다음 링크로 들어가 읽어볼 수 있다.

[generateData.js](https://github.com/johngrib/johngrib-jekyll-skeleton/blob/master/generateData.js)

이걸로 메타 데이터를 생성한 다음, 푸시할 때마다 같이 올려주면 된다.

생성하는 데이터 파일은 세 종류.

* `tagList.yml`: 모든 태그의 목록이다.
* `tagMap.yml`: 하나의 태그에 해당되는 문서의 파일명 목록을 갖는다.
* `pageMap.yml`: 모든 문서 파일의 파일명을 키로 갖고, 그에 대해 해당 문서의 프로퍼티를 값으로 갖는다.

상위 문서 목록은 다음과 같이 구현하였다.

```liquid
{% raw %}
{% if page.parent %}

    {% assign plist = page.parent | split: "," %}
    {% assign loop = "1234567890" | split: "" %}    <!-- 마음에 안 들지만 while 이 없어서 일단 이렇게 작성했다. -->
    {% assign pid = page.parent %}
    {% for item in loop %}

        {% if site.data.pageMap[pid] %}
            {% assign ancestor = site.data.pageMap[pid] %}
            {% assign pid = ancestor.parent %}
            {% assign plist = plist | push: pid %}
        {% endif %}

        {% if pid == 'index' %}
            {% break %}
        {% endif %}
    {% endfor %}

    {% assign plist = plist | reverse %}
        <p> 상위 문서:
    {% for item in plist %}
        {% assign p = site.data.pageMap[item] %}
            <a href="{{ p.url }}">{{ p.title }}</a>{% unless forloop.last %} - {% endunless %}
    {% endfor %}
        </p>
{% endif %}
{% endraw %}
```

### 태그 기능

태그 기능은 앞에서 만든 `generateData.js`로 생성한 데이터 파일을 사용하면 쉽게 구현할 수 있다.

자세한 내용은 [tag.html](https://github.com/johngrib/johngrib-jekyll-skeleton/blob/v1.0/tag.html) 참고.

### git hook으로 generateData.js 자동 실행하기

`generateData.js`를 일일이 실행하는 것은 귀찮은 일이다. git hook의 pre-commit을 사용하면 commit 전에 `generateData.js`를 실행하고, 데이터 파일을 add 할 수 있다.

블로그 경로 하위에 있는 `.git/hook/`에 다음과 같은 내용을 가진 `pre-commit` 파일을 추가해 주면 된다.

```sh
#!/bin/sh

./generateData.js
git add _data
```

이렇게 하면 commit 하기 전에 데이터 파일을 생성하고 add 까지 마치게 된다.


## 구현 결과

[https://johngrib.github.io/](https://johngrib.github.io/)

만들고 보니 썩 위키 비슷한 느낌이 난다. 마음에 든다.

## 실제로 어떻게 사용하나

이 가이드를 최초로 작성하고 나서 약 25개월이 지났다. 나는 아직까지 내 위키를 아주 잘 사용하고 있고, 내 일상의 주요한 도구 중의 하나가 되었다.

한편으로는 이 문서를 읽고 실천해보려는 분들의 다양한 질문을 받기도 했다. 안타깝게도 내가 직접 만나거나 메신저 등으로 직접 코칭을 해 드린 몇몇 분들 외에는 오래 사용하고 계신 분들이 많지 않은 것 같다.

따라서 내가 실제로 어떻게 사용하고 있는지를 추가하려 한다.

### 터미널을 연다

즐겁게 컴퓨터를 사용하다가 터미널을 열어주는 단축키를 입력한다. 그러면 터미널이 열린다.

### vim을 실행한다

터미널에서 `vim`을 입력하면 다음과 같은 startify 화면이 나온다.

* `0`을 누르면 위키가 열리게 되고, `1`을 누르면 로컬에서만 돌리고 있는 비공개 위키가 열리게 된다.

![start-with-startify]( /resource/wiki/my-wiki/startify.png )

### startify에서 위키를 선택한다

키보드에서 `0`을 누르거나, 커서를 `0`으로 옮기고 엔터를 누르면 다음과 같은 화면이 나온다.

* IDE의 프로젝트처럼 마지막으로 편집하고 있던 파일들이 버퍼로 로드되고 창 레이아웃이 자동으로 잡혀서 나오므로 전에 하던 작업을 이어서 할 수 있다.
* 오른쪽의 Tagbar에 있는 TOC(목차)를 보며 작업할 수 있다.
    * 목차에 있는 소제목을 더블 클릭하면 화면과 커서가 해당 소제목으로 점프한다.
    * 물론 커서를 소제목에 놓고 엔터를 쳐도 된다.

![main]( /resource/wiki/my-wiki/main.png )

### 글을 편집한 다음 발행한다

글을 편집하고 저장한 다음, git으로 `add`, `commit`, `push`를 해주면 `github.io`로 글이 배포된다. `generateData.js`는 git hook이 알아서 실행시켜 주기 때문에 따로 돌려주지 않아도 된다.

### index 파일을 오픈한다

새로운 글을 작성하는 것은 엄청나게 쉬운 일이다.

아무 문서에서나 새로운 문서의 타이틀 겸 파일 이름을 쓴 다음, 해당 타이틀에 커서를 놓고 엔터를 치면 된다. 그러면 파일이 생겨나고 기본 포맷이 입력된다.

* 나는 관리 편의를 위해 보통 `index.md` 파일에서 새로운 문서를 추가한다.
* `index.md`파일을 열기 위해 `fzf`로 파일을 탐색한다. 나는 `fzf` 파일 탐색을 `<f1><f1>`으로 등록해놨으므로 `f1`키를 두 번 누르면 파일 탐색이 시작된다.
    * 오른쪽에 파일 내용 미리보기도 나오므로 편리하다.

![fzf]( /resource/wiki/my-wiki/find-file.png )

* 적당히 파일 이름을 입력해 찾고 엔터를 누르면 파일이 열린다.

### 새로운 파일을 추가한다

시험삼아 `cargo` 항목을 추가하기로 하자.

* `rust`의 child 문서로 만들고 싶으므로, `start-rust-with-vim` 링크 아래쪽에 추가하기로 했다.
* 커서를 놓고 새로운 라인을 추가하면 `*`이 자동으로 추가된다.
* `cargo`라고 타이핑한다.

![]( /resource/wiki/my-wiki/new-link.png )

* normal 모드에서 엔터를 누르면 링크가 생긴다. 좌우에 생긴 두 개의 대괄호가 링크를 의미한다.

![]( /resource/wiki/my-wiki/new-cargo.png )

* 한 번 더 엔터를 누르면 새로 만들어진 파일로 이동하게 된다.

![]( /resource/wiki/my-wiki/new-file.png )

* 이 파일에 내용을 채우고 저장한 다음 git으로 `add`, `commit`, `push`를 하면 글이 발행된다.

### 파일 이름을 변경한다

`:vimwikire` 라고 쓰고 탭 키를 누르면 `:VimwikiRenameLink` 커맨드가 자동완성된다. 엔터를 누르고 `y`를 눌러 동의하면 새로운 이름을 물어보고, 위키 디렉토리 전체에서 해당 파일이 링크된 곳을 새로운 이름으로 바꿔준다.

### 파일을 삭제한다

`:vimwikidel` 이라 쓰고 탭 키를 누르면 `:VimwikiDeleteLink` 커맨드가 자동완성된다. 엔터를 누르고 `y`를 눌러 동의하면 현재 편집중인 문서 파일을 삭제하고, 위키 경로 전체에서 해당 파일 링크를 모두 해제해준다.

### 이미지를 추가한다

github의 issue 를 하나 열고 이미지를 붙여넣으면 다음과 같이 마크다운 형식의 이미지 링크 문자열이 생성된다.

![issue 화면]( /resource/wiki/my-wiki/82725036-3ce90680-9d15-11ea-939d-545c63dceec5.png )

이 문자열을 작성하고 있는 마크다운 파일에 붙여넣고 저장하면 문서에 이미지를 쉽게 추가할 수 있다.

github issue 에 이미지를 무한히 추가할 수 있다는 특징을 사용한 치팅이라 할 수 있다.

게다가 이 상태로 issue를 저장하지 않고 그대로 웹 브라우저를 닫아도 이미지 주소는 유효하다. 이미지 때문에 굳이 issue를 생성하지 않아도 된다는 점이 마음에 든다.

그런데 만약 github 규정이 바뀌게 된다면 이미지를 모두 잃을 수 있으므로, 문서를 나는 commit 할 때 pre-commit 훅으로 자동으로 다운로드하는 셸 스크립트를 만들어 사용하고 있다.

즉, 사용 흐름은 다음과 같다.

1. 내가 할 일
    1. 문서를 작성하던 도중, 이미지를 첨부할 일이 생겼다.
    2. github issue에 이미지를 붙여넣어, 이미지 링크를 생성한다.
    3. 문서를 마저 작성하고 저장한 다음, commit 한다.
2. pre-commit 훅이 하는 일
    1. pre-commit 훅이 새로 추가한 문서에 포함된 githubusercontent 링크를 탐지하여 파일을 로컬에 다운로드한다.
    4. 문서에 포함된 링크가 로컬에 다운로드한 파일을 바라보도록 변경된다.
    5. 변경된 파일이 다시 add 된 다음 commit 된다.

자세한 내용은 [save-images.sh]( https://github.com/johngrib/johngrib.github.io/blob/master/tool/save-images.sh )를 참고.

## 문제 해결
### :VimwikiRenameLink 가 parent 에는 작동하지 않는 문제

[honggaruy]( https://github.com/honggaruy )님이 [다음과 같은 질문을 남겨 주셨다]( https://github.com/johngrib/johngrib.github.io/issues/31#issuecomment-608995471 ).

> 사용중에 질문이 생겼습니다. 위에서 파일 이름을 변경한다는 부분에서 :VimwikiRenameLink를 layout이 Category 인 문서에서 실행시에 링크 걸린것들은 모두 바뀌는 것 같은데 각 문서페이지의 메타데이터에서 parent 요소까지는 안 바뀌는것 같더군요. 그래서 :VimwikiRenameLink 수행시 상위항목 표시가 모두 깨집니다. 혹시 해결책이 있으신가 해서 문의드립니다.

`:VimwikiRenameLink` 가 통하지 않는 이유는 다음과 같이 작성되었기 때문이다.

```markdown
parent : index
```

`:VimwikiRenameLink` 가 작동하도록 하려면 다음과 같이 `\[[`와 `]]`를 씌워주면 된다.

```markdown
parent : [[index]]
```

그런데 모든 문서에 대해 일일이 작업하는 것은 귀찮다. `sed`를 사용하면 깔끔하게 해결할 수 있다.

```sh
find . -name '*.md' \
  | xargs sed -E -i '' 's,(^parent *: *)([^\[]*)$,\1[[\2]],'
```

`ag`를 써서 다음과 같이 작업해도 같은 결과가 나올 것이다.

```sh
ag 'parent\s*:\s*[^\[\]]*$' -l \
  | grep '\.md$' \
  | xargs sed -E -i '' 's,(^parent *: *)([^\[]*)$,\1[[\2]],'
```

아무튼 이 작업을 하고 나면 다음과 같이 검증할 수 있을 것이다.

![]( /resource/wiki/my-wiki/parent-wrap.png )

```sh
 # 몇 개의 파일이 변경되었는지 확인한다
git status --short | awk '{print $1}' | sort | uniq -c
```

- 스크린샷을 보면 377 개의 파일이 변경되었음을 알 수 있다.

```sh
 # 파일별로 몇 줄이 추가되고 몇 줄이 삭제되었는지 확인한다
git diff --numstat | awk '{print $1, $2}' | sort | uniq -c
```

- 스크린샷을 보면 377 개의 파일이 각각 1 줄이 추가되고, 1줄이 삭제되었음을 알 수 있다.

이 작업을 마치면 `:VimwikiRenameLink`가 `parent`에도 잘 작동하게 된다.


## Links

* [https://johngrib.github.io/](https://johngrib.github.io/): 나의 Jekyll wiki.
* [https://github.com/johngrib/johngrib-jekyll-skeleton](https://github.com/johngrib/johngrib-jekyll-skeleton): 나의 Jekyll wiki skeleton. 새로운 위키를 만들고 싶다면 fork 하고 수정해서 쓰면 된다.
* [https://github.com/johngrib/johngrib.github.io](https://github.com/johngrib/johngrib.github.io): 나의 Jekyll wiki. skeleton으로 감이 안 잡힌다면 이걸 fork하고 받아서 수정해서 쓰면 된다.

* [Vimwiki](https://github.com/vimwiki/vimwiki)
* [Computer_science(wikipedia)](https://en.wikipedia.org/wiki/Computer_science)
* [Google Custom Search(구글 맞춤 검색)](https://www.google.co.kr/cse/)
* [Vim Tips Wiki - Insert current date or time](http://vim.wikia.com/wiki/Insert_current_date_or_time)

## Endnote

[^1]: `document.querySelectorAll('a').length`로 조사해보니 `1481`이 나왔다.
[^2]: [https://pages.github.com/versions/](https://pages.github.com/versions/)


