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
> 만약 마트업의 특정 부분에 대해서만 인터넷 익스플로러를 대상으로 하려면 인터넷 익스플로러에서 마이크로 소프트의 조건부 주석을 사용할 수 있습니다. 인터넷 익스플로러가 아닌 다른 브라우저는 이 독점적인 설명을 무시합니다.
>
> ```html
> <!--[if IE 8]-->
>     <insert IE specific markup here>
>     [endif]-->
> ```


