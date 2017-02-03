# 5장. jQuery 플러그인을 사용하여 개발 속도 향상하기

이전 장에서는 `MyPhoto`의 콘텐츠 스타일을 지정하는 방법과 다른 부트스트랩 컴포넌트를 사용하고, 커스터마이즈 하여 웹 사이트의 전반적인 모양을 개선하는 방법에 대해 설명했습니다. 부트스트랩의 내비게이션 바의 사용 방법, 아이콘 사용 방법, Scrollspy 플러그인을 사용하여 웹 사이트의 스크롤을 커스터마이즈 하는 방법을 배웠습니다. 이 장에서는 서드파티 플러그인의 힘을 강조하여 가장 보편적이고 평범한 기능들에 대한 개발 속도를 높이는 필수 서드 파티 플러그인을 소개할 것입니다. 이전 장 전반에 걸쳐 구현된 기능을 바탕으로 jQuery 브라우저 플러그인(`jquery.browser`)을 사용하여 클라이언트측 브라우저 탐지를 신속하고 효율적으로 구현하는 방법을 먼저 설명합니다. 그런 다음 먼저 부트스트랩의 페이지 매김(Pagination)을 다뤄서 페이지 매김에 의해 표 형식의 **Events** 섹션을 개선합니다. 그 다음 **bootpag**라는 페이지 매김 플러그인을 사용하여 기본 페이지 매김 기능을 빠르게 향상시키는 방법을 보여줍니다. 부트스트랩의 라이트박스(Lightbox)를 사용하여 이미지를 추가하면, **Events** 섹션이 더욱 개선됩니다. 마지막으로 우리는 표 형식의 데이터 표시를 유지하면서 jQuery의 `DataTables`를 사용하여 `MyPhoto`의 가격표를 개선합니다.

요약하자면, 이번 장에서는 다음의 내용을 다룹니다.

* jQuery 브라우저 플러그인을 사용하여 브라우저 감지
* bootpag를 사용하여 페이지 매김 개선
* 부트스트랩의 라이트박스를 사용하여 이미지 표시
* DataTables를 사용하여 테이블 형식의 데이터 표시 향상

## 브라우저 감지

**4장. 네비게이션, 푸터, 알림 및 콘텐츠**에서의 가상의 예제를 떠올려 보세요. `MyPhoto`는 특정 버전 이상의 브라우저만 지원합니다. 이를 위해 부트 스트랩 알림을 페이지에 추가하여 방문자에게 브라우저가 지원되지 않는다고 통보했습니다. 그러나 지금까지 우리는 `MyPhoto` 방문자가 어떤 브라우저 또는 어떤 브라우저 버전을 사용하고 있는지를 실제로 식별할 방법이 없었습니다. 알림을 숨기고 표시할 로직이 없기 때문에 사용자 브라우저가 실제로 웹사이트에서 지원되었는지의 여부에 관계없이 경고가 표시되었습니다. 이제 우리는 이 누락된 로직을 구현할 때가 되었습니다.

웹 브라우저는 HTTP **요청 헤더**(*그림 5.1* 참고)의 일부인 **User-Agent**라는 특수한 필드를 사용하여 이름과 버전 정보를 지정하여 스스로를 식별합니다. 자바스크립트를 사용하면, `window.navigator` 속성을 사용하여 이 필드에 접근할 수 있습니다. 이 정보에는 HTTP **요청 헤더**의 **User-Agent** 필드에 있는 동일한 문자열이 있습니다. 따라서 방문자의 브라우저가 실제로 지원되는지 여부를 확인하려면 지원되는 브라우저와 `window.navigator`에서 제공하는 문자열을 비교하면 됩니다. 그러나 *그림 5.1*에서 볼 수 있듯이 문자열은 대개 길고 복잡합니다. 결과적으로 서로 다른 브라우저를 맞추는 일은 지루하고 프로그래밍 오류가 발생할 수 있습니다. 따라서 이 일치하는 지에 대한 여부를 확인하는 작업을 수행하는 외부 리소스를 사용하는 것이 훨씬 낫고 잘 테스트 되고 잘 문서화 되어 있어 최신 상태로 유지가 됩니다. 널리 사용되는 jQuery  브라우저 플러그인([https://github.com/gabceb/jquery-browser-plugin](https://github.com/gabceb/jquery-browser-plugin))은 우리가 사용할 수 있는 리소스 중 하나입니다. 평소처럼 Bower를 사용하여 콘솔에서 플러그인을 설치합니다.

```bash
bower install jquery.browser
```

설치가 완료되면 아래에 새 디렉토리가 표시됩니다.

```bash
bower_components:

bower_components/jquery.browser
```

`dist` 폴더 안에 두 개의 파일 `jquery.browser.js`와 `jquery.browser.min.js`가 있어야 합니다.

다음 스크린샷을 살펴보기 바랍니다.

![](/assets/image_05_001.jpg)

*그림 5.1*: 요청할 때 HTTP 헤더의 일부로 전송 된 User-Agent 문자열의 예입니다.

앞서 언급한 파일을 볼 수 있다면, `jquery.browser`가 성공적으로 설치되었음을 의미합니다. 이제 `jquery.browser`를 사용하기 전에 먼저 경고에 몇 가지 할 일이 있습니다. 첫 번째로 할 일은 경고를 유일하게 식별하는 방법을 찾는 것입니다. 이렇게 하기 위해 HTML의 `id` 속성을 사용합니다. `id="unsupported-browser-alert"`의 형태로 `id` 속성을 `alert`에 추가합니다.

다음으로 마크업을 정리합니다. `css/myphoto.css`를 열고 인라인 스타일을 우리 스타일 시트로 이동합니다.

> **팁**
>
> 앞에서 설명했던 것 처럼 인라인 스타일은 많은 비용을 지불해야 합니다. 때때로 우리는 샘플 코드를 짧게 하기 위해 인라인 스타일을 적용할 수도 있지만 학습용 이외에서는 그렇게 하지 않는 것이 좋습니다.

이렇게 하면 브라우저 검색에 영향을 미치지 않으면서도 마크업을 깔끔하게 유지할 수 있습니다. 다음 코드를 살펴보세요.

```css
#unsupported-browser-alert {
    position: fixed;
    margin-top: 4em;
    width: 90%;
    margin-left: 4em;
}
```

> **참고**
>
> 일반적으로 CSS 룰과 `id`를 결합하는 것은 좋은 생각이 아닙니다. 강한 결합으로 재사용성이 좋지 않기 때문입니다. 이에 대해서는 **6장. 플러그인 사용자 정의**에서 더 자세하게 설명할 것입니다.

특정 조건(즉, 사용자가 특정 버전의 인터넷 익스플로러를 사용하는 경우)에서만 `alert`을 표시하기 때문에 기본적으로 `alert div`를 숨겨야 합니다. 다음 CSS를 `alert` 스타일에 추가하세요.

```css
display: none;
```

저장하고 새로 고침 합니다. 이제 페이지 상단의 경고가 더이상 표시되지 않습니다. 게다가 가장 바깥쪽의 `alert div`에는 `id`와 `class` 속성만 포함됩니다.

```html
<div class="alert alert-danger" id="unsupported-browser-alert">
    <a href="#" class="close" data-dismiss="alert" aria-label="close">&times;</a>
    <strong class="alert-heading"><i class="fa fa-exclamation"></i>Unsupported browser</strong> Internet Explorer 8 and lower are not supported by this website.
</div>
```

이제 마침내 새로 설치한 jQuery 플러그인을 사용할 때가 되었습니다. `MyPhoto`의 `index.html` 파일을 열고 문서의 `head`에 압축된 `jquery.browser` 자바스크립트 파일을 포함합니다.

```html
<script src="bower_components/jquery.browser/dist/jquery.browser.min.js"></script>
```

좋습니다. `index.html`의 `head`는 다음과 같이 보이게 될 것입니다.

```html
<head>
    <meta charset="UTF-8">
    <title>ch05</title>
    <link rel="stylesheet" href="bower_components/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="styles/myphoto.css" />
    <link rel="stylesheet" href="bower_components/components-font-awesome/css/font-awesome.min.css" />
    <script src="bower_components/jquery/dist/jquery.min.js"></script>
    <script src="bower_components/bootstrap/dist/js/bootstrap.min.js"></script>
    <script src="bower_components/jquery.browser/dist/jquery.browser.min.js"></script>
    <script type="text/javascript">
        $(document).ready(function () {
            $("nav ul li a").on('click', function (evt) {
                evt.preventDefault();
                var offset = $(this.hash).offset();
                if (offset) {
                    $('body').animate({
                        scrollTop: offset.top
                    });
                }
            });
        });
    </script>
</head>
```

이제 `jquery.browser`를 사용하여 방문자가 어떤 브라우저를 사용하는지 감지할 준비가 되었습니다. 이를 위해 그리거 `jquery.browser` 문서에 명시된 바와 같이 다음 변수를 사용할 수 있습니다.

* `$.browser.msie`: 웹사이트 방문자가 마이크로 소프트 인터넷 익스플로러를 사용하는 경우에 해당됩니다.
* `$.browser.webkit`: 웹사이트 방문자가 크롬, 사파리 또는 오페라를 사용하는 경우에 해당됩니다.
* `$.browser.version`: 웹사이트 방문자가 사용하고 있는 브라우저의 버전(유형이 아님!)을 보여줍니다.

경고를 표시하는 로직을 추가하려면 사용자가 인터넷 익스플로러를 사용하는지 여부에 대한 일반적인 조건부터 시작합니다. 나중에 사용자가 특정 버전의 인터넷 익스플로러를 사용하는지 여부에 관계없이 더 구체적인 조건으로 이동합시다. 즉, 방문자의 브라우저가 인터넷 익스플로러로 식별하는 경우에만 브라우저 경고를 표시하도록 하는 것으로 시작합니다. 이 코드는 매우 간단합니다. `$.browser.msie` 변수를 확인하기만 하면 됩니다. 이 변수가 `true`로 평가되면 jQuery를 사용하여 경고를 표시합니다.

```javascript
if ($.browser.msie) { 
    $('#unsupported-browser-alert').show(); 
}
```

이제 브라우저 테스트를 보다 구체적으로 만들어보겠습니다. 이제 방문자가 인터넷 익스플로러를 사용하고 있는지의 여부와 인터넷 익스플로러의 버전(버전 8이라고 가정해봅니다)이 `MyPhoto`에서 지원되지 않는지의 여부를 테스트 하려 합니다. 이렇게 하기 위해 `$.browser.version` 변수를 사용하여 두 번째 검사를 수행하기만 하면 됩니다. 조건부가 `true`로 평가되면 `show()` 함수가 실행됩니다. `show()` 함수는 요소의 표시 규칙을 수정하여 표시가 되도록 합니다.

```javascript
if ($.browser.msie && $.browser.version <= 8) { 
    $('#unsupported-browser-alert').show(); 
}
```

이 코드를 HTML 문서의 `head`에 삽입하세요.

```html
<script type="text/javascript">
    $(document).ready(function () {
        $("nav ul li a").on('click', function (evt) {
            evt.preventDefault();
            var offset = $(this.hash).offset();
            if (offset) {
                $('body').animate({
                    scrollTop: offset.top
                });

            }

        });
        if ($.browser.msie && $.browser.version <= 7) {
            $('#unsupported-browser-alert').show();
        }

    });
</script>
```

> **참고**
>
> 인터넷 익스플로러의 조건부 주석
>
> 만약 마크업의 특정 부분에 대해서만 인터넷 익스플로러를 대상으로 하려면 인터넷 익스플로러에서 마이크로 소프트의 조건부 주석을 사용할 수 있습니다. 인터넷 익스플로러가 아닌 다른 브라우저는 이 독점적인 설명을 무시합니다.
>
> ```html
> <!--[if IE 8]-->
>     <insert IE specific markup here>
>     [endif]-->
> ```

## bootpag를 사용하여 페이지네이션 향상시키기

이 섹션에서는 부트스트랩의 기본 페이지 매김(페이지네이션)을 사용하는 방법과 최소한의 노력으로 신속하게 한계를 극복하는 방법을 배우게 됩니다. 먼저 섹션에 샘플 이벤트 콘텐츠를 채운 다음 섹션의 전체 길이를 줄이기 위해 이벤트 페이지로 그룹화 합니다. **Events** 섹션에 이벤트 셋트를 추가하려면 `services-events div`의 `<p>Lorem Ipsum</p>` 마크업을 다음의 이벤트 플레이스 홀더 텍스트로 교체합니다.

```html
<h3>My Sample Event #1</h3>
<p>
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur leo dolor, fringilla vel lacus at, auctor finibus ipsum.
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi quis arcu lorem. Vivamus elementum convallis enim sagittis
    tincidunt. Nunc feugiat mollis risus non dictum. Nam commodo nec sapien a vestibulum. Duis et tellus cursus, laoreet
    ante non, mollis sem. Nullam vulputate justo nisi, sit amet bibendum ligula varius id.
</p>
```

이 텍스트를 세 번 반복하면 페이지의 **Events** 탭 아래에 세 개의 샘플 이벤트가 표시됩니다(*그림 5.2* 참조). 상위 패딩과 `2rem`의 왼쪽 여백을 상위 컨테이너에 추가하여 섹션을 덜 복잡하게 보이도록 이벤트들을 오프셋 합니다.

```css
#services-events .container {
    margin-left: 2rem;
    padding-top: 1rem;
}
```

다음 스크린샷을 살펴보기 바랍니다.

![](/assets/image_05_002.jpg)

*그림 5.2*: 세 가지 샘플 이벤트가 이벤트 탭 내에서 아래쪽으로 표시됩니다.

저장하고 새로고침을 하세요. 꽤 좋아 보입니다. 왜 우리는 정확히 분리된 페이지에 이벤트를 표시하려고 할까요? 우리가 점점 더 많은 이벤트들을 추가하기 시작할 때 이벤트들을 아래쪽으로 계속 나타날 것입니다. 따라서 **Events** 섹션은 무한정 커지게 됩니다. 페이지 매김은 모든 `MyPhoto`의 이벤트들을 나열하는 기능을 유지하면서 이 문제를 피할 수 있는 좋은 방법입니다. 부트스트랩은 정렬되지 않은 목록 요소에 `pagination` 클래스를 적용하여 페이지의 모든 섹션에 추가할 수 있는 시각적으로 매력적인 페이지 매김 스타일을 제공합니다(*그림 5.3* 참고). 이 정렬되지 않은 목록 안의 개별 목록 항목에 `page-item` 클래스가 적용되어야 합니다. 이 클래스를 적용하면 요소의 표시 속성이 `inline`으로 설정됩니다. `pagination` 클래스를 적용하면, 순서가 없는 목록의 표시가 `inline-block`으로 설정되고 여백이 조정됩니다. 따라서 10페이지 씩 페이지 매김을 표시하려면(앞으로 10페이지를 계속 사용합니다), 세 번째 이벤트의 `p` 요소 다음에 다음의 마크업을 추가하세요(`active` 클래스가 목록의 항목을 사용하여 현재 선택된 페이지를 나타낼 수 있습니다. `pagination-lg`와 `pagination-sm` 클래스를 사용하여 페이지네이션 콘트롤의 크기를 늘리거나 줄일 수 있습니다).

```html
<ul class="pagination">
    <li class="page-item"><a class="page-link active" href="#">1</a></li>
    <li class="page-item"><a class="page-link" href="#">2</a></li>
    <li class="page-item"><a class="page-link" href="#">3</a></li>
    <li class="page-item"><a class="page-link" href="#">4</a></li>
    <li class="page-item"><a class="page-link" href="#">5</a></li>
    <li class="page-item"><a class="page-link" href="#">6</a></li>
    <li class="page-item"><a class="page-link" href="#">7</a></li>
    <li class="page-item"><a class="page-link" href="#">8</a></li>
    <li class="page-item"><a class="page-link" href="#">9</a></li>
    <li class="page-item"><a class="page-link" href="#">10</a></li>
</ul>
```

> **참고**
>
> 부트스트랩 3에서의 페이지매김
>
> 페이지매김에 관해서는 부트스트랩 3에서 부트스트랩 4로의 변화가 그리 많지 않습니다. `pagination` 클래스는 두 버전 사이에 유지되고 있습니다. 그러나 부트스트랩 3에서는 어느 요소가 페이지 매김 항목이고 어느 요소가 페이지 매김 링크인지를 명시적으로 지정할 필요가 없었습니다. `page-item`과 `page-link` 클래스는 부트스트랩 4에서만 도입되었기 때문에 이전의 정렬이 되지 않은 리스트를 생성하고 `pagination` 클래스를 적용하여 페이지매김을 지정할 수 있습니다.
>
> ```html
> <ul class="pagination">
>     <li><a class="active" href="#">1</a></li>
>     <li><a href="#">2</a></li>
>     <li><a href="#">3</a></li>
> </ul>
> ```

앞의 마크업을 추가함으로써 이미 부트스트랩의 기본 페이지 매김 기능이 끝났습니다. 실제 페이지 매김의 구현은 우리에게 달려있습니다. 다음의 것을 포함하게 될 것입니다.

* 이벤트 페이지의 그룹화
* 현재 활성화 된 페이지 감지
* 현재 선택된 페이지에 따라 다양한 페이지의 가시성을 토글

이벤트가 10 페이지를 넘으면, 새 페이지와 새 목록 항목을 수동으로 페이지네이터에 추가해야 합니다. 이 모든 것에 대한 로직을 구현하는 것은 복잡하고 어렵지는 않습니다. 우리가 잘 알려진 유저 인터페이스 문제에 대한 해결책을 다시 창조할 필요는 없을 것입니다. 실제로 이벤트 페이지 매김의 개발을 빠르게 해줄 수 있는 서드파티 라이브러리가 존재합니다.

가장 인기있는 라이브러리 중 하나는 `jQuery.bootpag`입니다. jQuery 플러그인을 사용하면, 데이터에 페이지 매김을 할 수 있습니다. 불행하게도 bootpag(버전 1.0.7 이하)는 현재 부트스트랩 4를 지원하지 않으므로 약간의 조정이 필요합니다. 이 장에서 제공되는 모든 라이브러리와 마찬가지로 `jQuery.bootpag`도 무료이며, 라이센스 정보는 물론 소스코드도 GitHub([https://github.com/botmonster/jquery-bootpag](https://github.com/botmonster/jquery-bootpag))에서 사용할 수 있습니다. 다음 스크린 샷을 살펴보세요.

![](/assets/image_05_003.jpg)

*그림 5.3*: 부트스트랩 기본 페이지매김

당연히 `bootpag` Bower 패키지 이름도 bootpag입니다. 계속해서 설치하세요.

```bash
bower install bootpag
```

설치가 완료되면 `bower_components` 디렉토리 아래에 `bootpag` 디렉토리가 있어야 합니다. `bootpag/lib` 내부에는 다음과 같은 파일이 있습니다.

```
jquery.bootpag.js

jquery.bootpag.min.js
```

언제나처럼 우리는 플러그인의 축소된 버전으로 작업하려 합니다. 그래서 문서의 `head`에 `jquery.bootpag.min.js`를 포함합니다.

```html
<script src="bower_components/bootpag/lib/jquery.bootpag.min.js"></script>
```

bootpag를 사용하기 전에 플러그인에는 컨테이너가 필요하다는 것을 이해해야 합니다. 하나의 컨테이너에 페이지매김 콘트롤을 표시해서 하나의 컨테이너에 페이지매김을 표시할 수 있습니다. 즉, **Events** 섹션의 영역을 표시할 데이터와 사용자가 데이터를 탐색하는 콘트롤을 구분하는 영역으로 나누어야 합니다. 사용자가 패이지매긴 콘트롤을 사용하여 데이터를 탐색하면, 콘텐츠 영역이 새 콘텐츠로 업데이트 되거나 여러 컨테이너의 가시성이 토글이 됩니다.

우리는 후자의 방식을 사용할 것입니다. 먼저 이벤트를 페이지로 나누고 페이지매긴 콘트롤의 이벤트 리스너를 사용하여 다양한 페이지의 표시 여부를 전환합니다. 이를위해 **Services** 섹션에서 이벤트를 수정하여 각각의 이벤트가 고유한 페이지(`div` 사용)에 포함되도록 해야 합니다.

예제는 세 개의 샘플 이벤트로 구성되어 있으므로 두 개의 이벤트로 나눕니다. 첫 번째 페이지에는 **My Sample Event #1**와 **My Sample Event #2**를 포함하고 두 번째 페이지에서는 **My Sample Event #3**을 포함시킵니다. `div` 요소를 사용하여 각각의 페이지를 나타냅니다. 각각의 페이지의 `div`에는 고유한 `id`인 `page` 단어와 페이지 번호로 구성됩니다. 페이지매김 콘트롤은 마지막 이벤트 이후에 추가됩니다. 이렇게 하려면 마지막 페이지 아래에 페이지매김을 유지할 빈 `div`를 추가하세요. 고유한 `id`가 할당되어야 합니다.

```html
<div class="row">
    <div id="page-1">
        <h3>My Sample Event #1</h3>
        <p>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur leo dolor, fringilla vel lacus at, auctor finibus ipsum. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi quis arcu lorem. Vivamus elementum convallis enim sagittis tincidunt. Nunc feugiat mollis risus non dictum. Nam commodo nec sapien a vestibulum. Duis et tellus cursus, laoreet ante non,mollis sem. Nullam vulputate justo nisi, sit amet bibendum ligula varius id.
        </p>
        <h3>My Sample Event #2</h3>
        <p>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur leo dolor, fringilla vel lacus at, auctor finibus ipsum. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi quis arcu lorem. Vivamus elementum convallis enim sagittis tincidunt. Nunc feugiat mollis risus non dictum. Nam commodo nec sapien a vestibulum. Duis et tellus cursus, laoreet ante non,mollis sem. Nullam vulputate justo nisi, sit amet bibendum ligula varius id.
        </p>
    </div>
    <div id="page-2">
        <h3>My Sample Event #3</h3>
        <p>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur leo dolor, fringilla vel lacus at, auctor finibus ipsum. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi quis arcu lorem. Vivamus elementum convallis enim sagittis tincidunt. Nunc feugiat mollis risus non dictum. Nam commodo nec sapien a vestibulum. Duis et tellus cursus, laoreet ante non,mollis sem. Nullam vulputate justo nisi, sit amet bibendum ligula varius id.
        </p>
    </div>
    <div id="services-events-pagination"></div>
</div>
```

페이지매김 콘트롤을 실제로 사용하려면 먼저 컨테이너의 `bootpag`를 알려야 합니다. 요소의 `bootpag` 함수를 호출하여 원하는 페이지 수(이 경우 10)를 포함하는 매개 변수로 구성된 객체를 전달합니다. HTML 문서의 `head`에 다음 코드를 삽입하세요.

```javascript
$('#services-events-pagination').bootpag({
    total: 10
}).on("page", function (event, num) {});
```

`bootpag` 함수는 `id`가 `services-events-pagination`인 요소로 콘트롤을 렌더링 하지만 `page`를 매개변수로 하는 `on` 이벤트 리스너를 확인합니다. 이 이벤트 리스너는 사용자가 페이지 변경 콘트롤을 사용하여 페이지를 변경할 때 현재 비어있는 콜백 내에 포함된 코드를 호출합니다. 그러나 개별 페이지의 가시성을 토글하는 페이지 변경 로직을 구현하기 전에 우선 페이지를 숨겨야 합니다. 이렇게 하기위해 `myphoto.css` 파일을 업데이트 해야 합니다.

이제는 개별 페이지의 각 스타일을 `id`로 식별하여 스타일을 추가하는 방법이 있습니다. 이렇게 하면 이벤트가 늘어남에 따라 새로운 CSS 규칙을 추가해야 하기 때문에 스타일 시트를 굉장히 크게 만들게 될 것입니다. 훨씬 더 깔끔한 접근법은 컨테이너 내에 페이지를 포장한 다음 CSS 셀렉터를 사용하여 콘텐츠 영역 내의 모든 페이지(`div` 요소)를 숨기는 것입니다. 이렇게 하려면 새로운 `container div` 안에 페이지를 랩핑하고 이 `container`에 고유한 `id`를 할당합니다.

```html
<div id="services-events-content">
    <div id="page-1">
        <h3>My Sample Event #1</h3>
        <p>...</p>
        <h3>My Sample Event #2</h3>
        <p>...</p>
    </div>
    <div id="page-2">
        <h3>My Sample Event #3</h3>
        <p>...</p>
    </div>
</div>
```

> **참고**
>
> 알고 계신가요?
>
> 앞서 언급한 코드를 구현하는 쉬운 방법이 있습니다. 부트스랩의 `hide` 클래스를 사용하여 콜백에서 토글하게 할 수 있습니다. 이 문제는 직접 해보세요.

그런 다음 새 컨테이너 안에 있는 개별 페이지의 `div` 요소가 기본적으로 숨겨질 수 있도록 스타일 시트를 업데이트 합니다.

```css
#services-events-content div {
    display: none;
}
```

저장하고 새로고침을 누르세요. 모든 이벤트들이 숨겨져 있어야 합니다.

이제 사용자가 탐색을 할 때 개별 페이지를 볼 수 있게 하는 로직을 구현하면 됩니다. 이를 위해 현재 비어있는 콜백 함수를 완성하여 모든 페이지를 숨기고 현재 선택된 페이지만 표시되도록 합니다. 이전 페이지 대신 모든 페이지를 숨기면 이전에 선택한 페이지를 결정하는 로직이 필요하지 않으므로 코드를 훨씬 깔끔하게 정리할 수 있습니다. 대신 CSS 셀렉터를 사용하여 `services-events-content` 컨테이너에 포함된 모든 `div` 요소를 숨깁니다. `bootpag` 플러그인은 콜백 함수에 전달 된 두 번째 매개 변수(여기에서는 `num`)를 통해 현재 선택된 페이지 번호를 알려줍니다. 따라서 이 페이지 번호를 사용하여 표시하려는 `div`(페이지)의 `id`를 구성할 수 있습니다.

```javascript
$('#services-events-pagination').bootpag({
    total: 10
}).on("page", function (event, num) {
    $('#services-events-content div').hide();
    var current_page = '#page-' + num;
    $(current_page).show();
});
```

스타일 시트가 모든 페이지를 숨기는 방법을 보면서 사용자가 처음 페이지를 방문할 때 첫 페이지를 볼 수 있도록 해주는 구문을 포함시켜야 합니다. 이렇게 하려면 문서의 `head`에 `$('#page-1').show();`를 추가하면 됩니다. 그래서 다음과 같은 구조의 코드가 됩니다.

```javascript
$('#page-1').show();
$('#services-events-pagination').bootpag({
    total: 10
}).on("page", function (event, num) {

    // Pagination logic

});
```

다음 스크린 샷을 살펴보기 바랍니다.

![](/assets/image_05_004.jpg)

*그림 5.4*: `bootpag` 페이지네이션 콘트롤의 표시는 부트스트랩 4에서 깨져버렸습니다. 이것은 부트스트랩 4에 도입된 페이지네이션 콘트롤의 변경 때문입니다.

저장하고 새로고침을 하세요. 페이지매김 콘트롤이 작동하는 동안 화면이 깨집니다(*그림 5.4* 참고). 이것은 이전에 설명한 부트스트랩 4에 의해 도입된 페이지매김 콘트롤의 변경 때문입니다. `jquery.bootpag.js`를 살펴보면 130번 라인의 페이지매김 목록 항목을 구성하는데 있음을 알 수 있습니다. 다음 코드를 살펴보세요.

```javascript
return this.each(function () {
    var $bootpag, lp, me = $(this),
        p = ['<ul class="', settings.wrapClass, ' bootpag">'];

    if (settings.firstLastUse) {
        p = p.concat(['<li data-lp="1" class="', settings.firstClass,
            '"><a href="', href(1), '">', settings.first, '</a></li>'
        ]);
    }
    // ... 
});
```

부트스트랩 3에 대한 페이지매김 항목이 만들어지므로 항목을 생성하는 코드가 `page-item` 및 `page-link` 클래스를 적용하지 못하는 문제가 여기에 있었습니다. 우리는 이것을 충분히 쉽게 고칠 수 있습니다. 먼저 프로젝트 루트에 새 폴더인 `js`를 만들고 `jquery.bootpag.js` 파일을 폴더에 복사합니다. `page-item`과 `page-link` 클래스가 목록 항목 및 앵커 요소에 적용될 수 있도록 페이지 매긴 생성 로직을 업데이트 합니다.

```javascript
return this.each(function(){ 
    var $bootpag, lp, me = $(this), 
    p = ['<ul class="', settings.wrapClass, ' bootpag">']; 

    if(settings.firstLastUse){ 
        p = p.concat(['<li data-lp="1" class="page-item ', settings.firstClass, '"><a lass="page-link" href="', href(1),
        '">', 
        settings.first, '</a></li>']); 
    } 

    if(settings.prev){ 
        p = p.concat(['<li data-lp="1" class="page-item ', 
        settings.prevClass, '"><a class="page-link" href="', href(1),
        '">', 
        settings.prev, '</a></li>']); 
    }

    for(var c = 1; c <= Math.min(settings.total, settings.maxVisible); c++){ 
        p = p.concat(['<li class="page-item" data-lp="', c, '"><a 
        class="page-link" href="', href(c), '">', c, '</a></li>']); 
    } 

    if(settings.next){ 
        lp = settings.leaps && settings.total > settings.maxVisible 
        ? Math.min(settings.maxVisible + 1, settings.total) : 2; 
        p = p.concat(['<li data-lp="', lp, '" class="page-item ', 
        settings.nextClass, '"><a class="page-link" href="', href(lp), 
        '">', settings.next, '</a></li>']); 
    }
    
    if(settings.firstLastUse){ 
        p = p.concat(['<li data-lp="', settings.total, '" class=
        "page-item 
        last"><a class="page-link" href="', href(settings.total),'">',
        settings.last, '</a></li>']); 
    }

});
```

마지막으로 수정된 버전의 `bootpag`를 가리키도록 문서 헤드의 참조를 업데이트 합니다.

```html
<script src="js/jquery.bootpag.js"></script>
```

다음 스크린샷을 살펴보기 바랍니다.

![](/assets/image_05_005.jpg)

*그림 5.5*: 수정된 버전의 bootpag 플러그인을 사용한 페이지네이션

두 번째 페이지에 페이지를 매기면서 마지막 페이지에는 한 번만 이벤트가 포함될 수 있으며(**My Sample Event #3** 처럼) 이벤트 설명의 길이가 다릅니다. 따라서 사용자가 페이지를 전환할 때 높이가 달라질 수도 있습니다. 그래서 페이지매김 콘트롤 `div`가 위 아래로 움직이게 됩니다(*그림 5.6* 참고). 다행히도 이를 해결하려면, `event-services-content div`에 고정 높이인 `15em`을 할당해야 합니다. `myphoto.css`를 열고 다음을 추가하세요.

```css
#services-events-content { 
    height: 15em; 
}
```

다음 그림을 살펴보세요.

![](/assets/image_05_006.jpg)

*그림 5.6*: 두 페이지 사이의 높이가 차이나는 점을 확인하세요. 페이지당 이벤트 수가 다르거나 설명이 다른 이벤트를 나열하면 컨테이너 높이가 늘어나거나 줄어들 수 있습니다.

이제 `events` 컨테이너의 높이가 고정이 되어 컨테이너의 내용에 따라 컨테이너가 축소되지 않을 것입니다. 결과적으로 페이지매김 콘트롤은 고정된 위치에서 유지됩니다. 그러나 긴 이벤트 설명에 대해서 최종적인 문제가 나타납니다. `events` 컨테이너에서 허용하는 것 보다 많은 텍스트를 포함하는 이벤트들을 어떻게 처리하면 좋을까요? 예를 들어 *그림 5.7*의 **My Sample Event #2**에 추가된 단락을 고려해봅니다. 보시다시피 페이지매김 콘트롤이 이벤트 설명 위로 렌더링이 되고 있습니다.

컨테이너 높이를 초과하는 텍스트를 단순히 잘라냅니다.

![](/assets/image_05_007.jpg)

*그림 5.7*: 버그 표시- 긴 이벤트 설명으로 인해 페이지매김 콘트롤이 이벤트 설명 위로 렌더링 됩니다. 컨테이너 높이를 초과하는 텍스트는 잘라냅니다.

다시 한번 이번 수정은 한 줄로 간단하며 컨테이너의 모든 내용을 스크롤 할 수 있도록 컨테이너의 *Y*축 오버 플로우를 설정하는 작업이 포함됩니다. `myphoto.css`를 열고 `overflow-y` 속성에 `scroll`을 설정하여 `services-events-content` 컨테이너 스타일을 업데이트 합니다.

```css
#services-events-content {
    height: 15em;
    overflow-y: scroll;
}
```

저장하고 새로고침 하세요.

## 부트스트랩 라이트박스를 사용한 이미지 표시

**Events** 섹션에 없는 중요한 기능 중 하나는 이벤트를 설명하는 이미지를 포함하여 추가적인 정보를 제공하는 기능입니다. 물론 `img` 태그를 사용하여 이미지를 추가할 수는 있지만, 실용적이지는 않습니다. 이미지 크기가 컨테이너의 크기에 따라 제한되기 때문입니다.

이 섹션에서는 사용자가 이미지를 클릭할 때, 이미지를 페이지에서 리다이렉션 하지 않고 이미지를 확대할 수 있도록 함으로써 이러한 제한을 극복할 수 있는 방법을 보여줍니다. 이를 위해 각 이벤트마다 하나의 이미지를 삽입합니다(*그림 5.8* 참조). 각 이미지는 이벤트 설명의 왼쪽에 정렬이 되며 너비는 80이고 높이는 45입니다.

```html
<div id="page-1">
    <h3>My Sample Event #1</h3>
    <p>
        <img src="images/event1.jpg" align="left" width="80" height="45" /> 
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur leo dolor, fringilla vel lacus at, auctor finibus ipsum. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi quis arcu lorem. Vivamus elementum convallis enim sagittis tincidunt. Nunc feugiat mollis risus non dictum. Nam commodo nec sapien a vestibulum. Duis et tellus cursus, laoreet ante non, mollis sem. Nullam vulputate justo nisi, sit amet bibendum ligula varius id.
    </p>
    <h3>My Sample Event #2</h3>
    <p>
        <img src="images/event2.jpg" align="left" width="80" height="45" />
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur leo dolor, fringilla vel lacus at, auctor finibus ipsum. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi quis arcu lorem. Vivamus elementum convallis enim sagittis tincidunt. Nunc feugiat mollisrisus non dictum. Nam commodo nec sapien a vestibulum. Duis et tellus cursus, laoreet ante non, mollis sem. Nullam vulputate justo nisi, sit amet bibendum ligula varius id.
    </p>
</div>
```

이전 마크업을 저장하고 페이지를 새로고침하면 이미지가 텍스트와 잘 정렬되지 않은 상태로 보일 것입니다. 이것들은 좀 엉망으로 보입니다. 이미지, 텍스트 및 컨테이너 상단 사이의 간격을 추가하여 모양을 개선할 수 있습니다. 이렇게 하려면 `services-events-content` 컨테이너 안의 각각의 이미지에 `0.5em`위 위쪽 마진과 `1em`의 오른쪽 마진을 추가합니다.

```css
#services-events-content div img { 
    margin-top: 0.5em; 
    margin-right: 1em; 
}
```

> **참고**
>
> 알고 있나요?
>
> 특정 클래스를 사용하여 앞에서 언급한 문제를 해결할 수 있습니다(실제로도 더 깔끔한 방법일 수 있습니다). 혼자서 이러한 룰을 만들어보세요.

다음 스크린 샷을 살펴보기 바랍니다.

![](/assets/image_05_008.jpg)

*그림 5.8*: 각 이벤트의 설명과 함께 제공된 샘플 이미지. 이 이미지들은 좌측으로 정렬되며 80 x 45 크기입니다.

사용자가 이벤트 설명에 포함된 이미지를 확대할 수 있게 해주는 서드파티 라이브러리는 [https://github.com/jbutz/bootstrap-lightbox](https://github.com/jbutz/bootstrap-lightbox) GitHub에서 제공되는 부트스트랩 라이트박스입니다. 불행히도 이 플러그인은 더이상 유지보수 되지 않고 여러 가지 수정되지 않은 버그와 유용성에 문제가 있습니다. 다운로드하자마자 그것이 즉시 작동하지 않는 다는 것을 알게 될 것입니다. 운좋게도 DJ Interative([http://djinteractive.co.uk/](http://djinteractive.co.uk/))는 원래 부트스트랩 라이트 박스를 확장했습니다. 또한 GitHub([https://github.com/djinteractive/Lightbox-for-Bootstrap](https://github.com/djinteractive/Lightbox-for-Bootstrap))를 통해 사용할 수 있으며, 플러그인은 Creative Commons Attribution 2.5 라이센스를 따라 게시됩니다. 즉, 플러그인 작성자가 올바르게 기여한 조건에서 플러그인을 무료로 사용할 수 있습니다.

부트스트랩용 라이트박스를 다운로드하고 자바스크립트 파일과 CSS 파일을 HTML 문서의 `head` 안에 포함시키세요.

```html
<script src="bower_components/lightbox-for-bootstrap/js/bootstrap.lightbox.js"></script>
<link rel="stylesheet" href="bower_components/bootstrap-lightbox/css/bootstrap.lightbox.css" />
```

다행히도 라이트박스 내에 이미지를 표시하기 위해 플러그인을 사용할 때 기존 마크업을 거의 수정할 필요가 없습니다. 수행할 두 단계는 다음과 같습니다.

1. 기존의 `img` 요소를 `thumbnail` 클래스와 `data-toggle` 어트리뷰트를 가진 컨테이너 요소 안에 배치합니다.
2. `thumbnail` 클래스와 `data-target` 속성을 `img` 요소에 적용합니다.
    ```html
<p>
    <span class="thumbnails" data-toggle="lightbox"> 
        <img src="images/event1.jpg" align="left" width="80" height="45" class="thumbnail" data-target="images/event1.jpg"/> 
    </span> 
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Curabitur leo dolor,fringilla vel lacus at, auctor finibus ipsum. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi quis arcu lorem. Vivamus elementum convallis enim sagittis tincidunt. Nunc feugiat mollis risus non dictum. Nam commodo nec sapien a vestibulum. Duis et tellus cursus, laoreet ante non, mollis sem. Nullam vulputate justo nisi, sit amet bibendum ligula varius id.
</p>
    ```

`data-target` 어트리뷰트는 더 큰 이미지가 있는 것을 라이트박스에게 알려줍니다. 이미지 요소의 `src` 어트리뷰트는 라이트 박스 내에 표시될 이미지에 영향을 주지 않습니다. `data-target` 어트리뷰트만이 이를 판별합니다. 따라서 썸네일 이미지를 표시할 수 있습니다. 이 이미지는 다른 라이트박스 이미지에 실제로 연결됩니다. `data-toggle` 속성은 라이트박스 토글로 사용되는 요소를 식별하는 데 사용됩니다. 이 경우 토글은 80 x 45 크기의 이미지입니다. 그러나 토글은 이미지 요소일 필요가 없다는 점에 유의해야 합니다. 모든 요소는 라이트박스의 토글이 될 수 있습니다.

마지막으로 `thumbnails` 클래스는 `lightbox` 플러그인의 셀렉터 역할을 합니다. 이러한 기능이 없으면 원하는 기능이 작동하지 않습니다. 다음 스크린샷을 살펴보세요.

![](/assets/image_05_009.jpg)

*그림 5.9*: 부트스트랩의 라이트박스를 사용하여 확대된 이미지가 표시됩니다.

요약하면 **Events** 섹션은 다음과 같습니다.

```html
<div class="container">
    <div class="row" style="margin: 1em;">
        <div id="services-events-content">
            <div id="page-1">
                <h3>My Sample Event #1</h3>
                <p>
                    <span class="thumbnails" data-toggle="lightbox"> 
                        <img src="images/event1.jpg" align="left" width="80" height="45" class="thumbnail" data-target="images/event1.jpg"/> 
                    </span>
                    Lorem ipsum...
                </p>
                <h3>My Sample Event #2</h3>
                <p>
                    <span class="thumbnails" data-toggle="lightbox"> 
                        <img src="images/event2.jpg" align="left" width="80" height="45" class="thumbnail" data-target="images/event2.jpg"/> 
                    </span>
                    Lorem ipsum...
                </p>
            </div>
            <div id="page-2">
                <h3>My Sample Event #3</h3>
                <p>
                    <span class="thumbnails" data-toggle="lightbox"> 
                        <img src="images/event3.jpg" align="left" width="80" height="45" class="thumbnail" data-target="images/event3.jpg"/> 
                    </span>
                     Lorem ipsum...
                </p>
            </div>
        </div>
        <div id="services-events-pagination"></div>
    </div>
</div>
```

## DataTables로 가격 리스트 개선하기

**Events** 섹션을 그대로 두고 **2장. 스타일 구문 만들기**와 **3장. 레이아웃 구축하기**에서 작성한 가격 목록으로 이동할 시간입니다. 현재 표시된 데이터의 경우 기존 테이블 구조가 완벽하게 작동합니다. 가격은 멋지게 제시되고 테이블도 빽빽하지 않고 좋습니다. 그러나 `MyPhoto`가 수백가지의 가격을 표시해야 한다면(네. 이 경우에는 당연한 것으로 보일 수 있지만 데모 목적으로 사용하지 마세요), 우리듸 기존 테이블 구조는 디스플레이 용량을 훨씬 초과합니다. 열이 너무 많아서 테이블을 체계적으로 유지하는 데 도움이 되는 몇 가지 형식의 페이지매김을 구현해야 합니다. 물론 이전 섹션을 읽은 경우 서드파티 플러그인을 사용하여 페이지매김을 구현하는 것이 얼마나 쉬운 지 알 수 있습니다. 그러나 수백 또는 수천 개의 항목을 사용하면, 페이지 매김을 통한 웹사이트를 사용할 수 없게 됩니다. 사용자는 표 형식의 데이터를 필터링 하는 기능이나 표의 특정 항목을 검색하는 기능과 같은 고급 기능을 필요로 할 수 있습니다. 또한 사용자는 페이지 당 표시되는 테이블 항목 수를 조정할 수 있습니다. 이러한 모든 요구 사항은 테이블 구현을 매우 복잡하고 어렵게 만듭니다. 그러나 이러한 사용자 요구사항에 대해서는 일반적으로 잘 이해되고 있고 연구가 되어 있습니다. 이 때문에 우리 `MyPhoto`의 가격 목록을 향상시키기 위해 사용할 수 있는 우수한 타사 라이브러리가 있습니다. DataTables([https://www.datatables.net/](https://www.datatables.net/))를 만나세요. DataTables는 부트스트랩 스타일을 포함하는 jQuery 플러그인으로 이전에 언급한 모든 기능을 제공합니다.

DataTables를 사용하려면 DataTables 웹사이트를 통해 커스터마이즈 한 자신만의 빌드를 만들거나(정교한 다운로드 빌더를 제공) Bower를 사용할 수 있습니다.

```bash
bower install DataTables
```

설치가 완료되면 `bower_components/DataTables` 디렉토리를 찾아야 합니다.

이 디렉토리 안에는 `media/`가 자바스크립트 파일과 CSS 파일을 포함하고 있습니다. 이 파일들을 문서의 헤드에 포함시킬 수 있습니다. 특히 디렉토리에는 일반적인 jQuery 플러그인과 스타일은 물론 부트스트랩 관련 스타일이 포함됩니다.

* `dataTables.bootstrap.min.js`
* `jquery.dataTables.min.js`
* `dataTables.bootstrap.min.css`

파일을 통합해 보겠습니다.

```html
<script src="bower_components/DataTables/media/js/jquery.dataTables.min.js"></script> 
<script src="bower_components/DataTables/media/js/dataTables.bootstrap.min.js"></script> 
<link rel="stylesheet" href="bower_components/DataTables/media/css/dataTables.bootstrap.min.css" /> 
```

새롭게 포함 된 `dataTables`에 들어가기 전에 출력 크기와 가격을 재구성해야 합니다. 이전과 같은 데이터셋을 사용하여 테이블을 생성하세요. 단 간단하게 하기 위해 하나의 가격 세트만 표시할 수 있습니다.

```html
<table id="services-prints-table">
    <thead>
        <tr>
            <th> Extra Large</th>
            <th>Large</th>
            <th>Medium</th>
            <th>Small</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>24x36 (€60)</td>
            <td>19x27 (€45)</td>
            <td>12x18 (€28)</td>
            <td>6x5 (€15)</td>
        </tr>
        <tr>
            <td>27x39 (€75)</td>
            <td>20x30 (€48)</td>
            <td>16x20 (€35)</td>
            <td>8x10 (€18)</td>
        </tr>
        <tr>
            <td>27x40 (€75)</td>
            <td>22x28 (€55)</td>
            <td>18x24 (€40)</td>
            <td>11x17 (€55)</td>
        </tr>
    </tbody>
</table>
```

앞의 표는 헤더와 본문이 있는 표준 HTML입니다. 저장하고 새로고침 하세요. 좋습니다. 이제 우리에게 평이하고 간단한 테이블이 생겼습니다. 다음으로 진행할 것은 스타일을 지정하는 것입니다. 먼저 `width`를 `100%`로 만들어서 사용할 수 있는 전체 공간을 모두 사용하도록 테이블을 설정합니다. 그런 다음 `table` 및 `table-striped` 두 개의 부트스트랩 클래스를 적용합니다. 전자인 `table` 클래스는 패딩, 선 높이 및 수직 정렬을 조정하여 테이블에 기본적인 표 스타일을 적용합니다. 후자인 `table-striped` 클래스는 개별 행의 색상을 번갈아가며 변경합니다.

```html
<table id="services-prints-table" class="table table-striped" width="100%">
    <thead>
        <!-- Content here-->
    </thead>
    <tbody>
        <!--Content here-->
    </tbody>
</table>
```

데이터 테이블을 초기화 하려면 한 줄의 코드만 있으면 됩니다.

```javascript
$('#services-prints-table').DataTable();
```

저장하고 새로고침을 합니다. 즉시 테이블이 컨테이너 외부로 넘치는 것을 볼 수 있습니다. 이 문제를 해결하려면 `table` 요소를 `div`로 감싸고 `div`에 최대 너비 90%를 지정합니다(데모용으로 인라인 스타일을 사용하지만, 항상 인라인 스타일 사용은 지양해야 합니다).

```html
<div style="max-width: 90%;"> 
    <table id="services-prints-table" class="display">...</table> 
</div>
```

다시 한번 저장하고 새로고침 합니다. 테이블은 잘 보입니다(*그림 5.10* 참고). 이 테이블을 가지고 한 번 사용해 보세요. 검색 창을 사용하여 특정한 행을 필터링 하거나 행을 추가하고 표가 페이지로 연결되는 것을 확인해보세요. 추가적인 작업 없이도 페이지 당 표시할 항목 수를 제어할 수도 있습니다. 다음 스크린 샷을 살펴보세요.

![](/assets/image_05_010.jpg)

*그림 5.10*: datatables 가격 목록에 부트스트랩이 아닌 변형입니다. 깨지 페이지 매김 표시에 유의하세요.

`bootpag`에서와 마찬가지로 `dataTable` 플러그인이 부트스트랩 3의 페이지매김 방식을 적용한다는 점에 유의하기 바랍니다. 플러그인이 부트스트랩 4와 호환될 수 있도록 하려면 먼저 `dataTables.bootstrap.js` 파일을 `js` 폴더(`bootpag` 수정 시 작성한 폴더)에 복사해 넣고 63번 라인으로 이동하세요. `switch` 문은 사용할 목록 아이템의 클래스를 결정합니다. `switch`문 바로 뒤에 다음 줄을 추가하세요.

```javascript
btnClass += 'page-item table-page-item';
```

또한 `page-link` 클래스를 포함하도록 앵커 요소를 생성하는 109번 라인을 업데이트 합니다.

```javascript
.append($('<a>', {
        'class': 'page-link',

        'href': '#',

        'aria-controls': settings.sTableId,
        'data-dt-idx': counter,
        'tabindex': settings.iTabIndex
    })
    .html(btnDisplay)
)
```

`page-item` 클래스와 함께 `table-page-item` 클래스를 추가하는 방법에 유의하기 바랍니다. 이제 다음과 같이 정의하도록 합니다.

```css
.table-page-item {
    margin-left: 0.5rem;
}
```

다음 스크린샷을 살펴보기 바랍니다.

![](/assets/image_05_011.jpg)

*그림 5.11*: 고정된 DataTable, 페이지매김 콘트롤 및 이용 약관에 대한 레이블 적용

이 섹션을 끝내기 위해 다음과 같이 완전한 가격 섹션을 작성합니다.

```html
<div class="container">
    <h1 class="hidden-md">Our Print Sizes</h1>
    <div style="max-width: 90%;">
        <table id="services-prints-table" class="table table-striped" width="100%">
            <thead>
                <tr>
                    <th>Extra Large</th>
                    <th>Large</th>
                    <th>Medium</th>
                    <th>Small</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>24x36 (€60)</td>
                    <td>19x27 (€45)</td>
                    <td>12x18 (€28)</td>
                    <td>6x5 (€15)</td>
                </tr>
                <tr>
                    <td>27x39 (€75)</td>
                    <td>20x30 (€48)</td>
                    <td>16x20 (€35)</td>
                    <td>8x10 (€18)</td>
                </tr>
                <tr>
                    <td>27x40 (€75)</td>
                    <td>22x28 (€55)</td>
                    <td>18x24 (€40)</td>
                    <td>11x17 (€55)</td>
                </tr>
            </tbody>
        </table>
    </div>
</div>
```

## 요약

이 장에서는 타사 라이브러리를 사용하여 일반적인 사용자 인터페이스의 개발 요구 사항을 해결하는 법에 대해 배웠습니다. 이전 장에서 다양한 기능들을 향상시킴으로써 jQuery 브라우저 플러그인을 사용하여 사용자의 브라우저와 버전을 감지하는 방법을 보여주었습니다. 우리는 사용자가 데이터를 탐색할 수 있도록 `bootpag` 플러그인을 사용했습니다. 이와 함께 이미지 및 라이트박스를 사용하여 부트스트랩에 대한 **Events** 섹션의 모양과 느낌을 향상시켰습니다. 또한 DataTables를 사용하여 가격 목록의 사용성을 개선하는 방법에 대해서도 살펴 보았습니다.

널리 사용되는 타사 라이브러리를 사용하는 방법에 대한 지식으로 무장한 우리는 이제 우리의 정확한 요구에 맞게 부트스트랩의 jQuery 플러그인을 커스터마이징 할 준비가 되었습니다. 다음 장으로 이동하세요.



