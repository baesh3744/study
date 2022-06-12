# position

CSS position 속성은 문서 상에 요소를 배치하는 방법을 지정합니다.

```css
position: <value>;
```

## `<value>`

### static (default)

요소를 일반적인 문서 흐름에 따라 배치합니다. `top`, `right`, `bottom`, `left`, `z-index` 속성이 아무런 영향도 주지 않습니다.

### relative

요소를 일반적인 문서 흐름에 따라 배치하고, 자기 자신을 기준으로 `top`, `right`, `bottom`, `left`의 값에 따라 offset을 적용합니다. Offset은 다른 요소에는 영향을 주지 않습니다. 따라서 페이지 레이아웃에서 요소가 차지하는 공간은 `static`일 때와 같습니다.

`z-index` 값이 `auto`가 아니라면 새로운 쌓임 맥락을 생성합니다. `table-*-group`, `table-row`, `table-column`, `table-cell`, `table-caption` 요소에 적용했을 때의 작동 방식은 정의되지 않았습니다.

### absolute

요소를 일반적인 문서 흐름에서 제거하고, 페이지 레이아웃에 공간도 배정하지 않습니다. 대신 가장 가까운 위치 지정 조상 요소에 대해 상대적으로 배치합니다. 단, 조상 중 위치 지정 요소가 없다면 초기 컨테이닝 블록을 기준으로 삼습니다. 최종 위치는 `top`, `right`, `bottom`, `left` 값이 지정합니다.

`z-index` 값이 `auto`가 아니라면 새로운 쌓임 맥락을 생성합니다. 절대 위치 지정 요소의 바깥 여백은 서로 상쇄되지 않습니다.

### fixed

요소를 일반적인 문서 흐름에서 제거하고, 페이지 레이아웃에 공간도 배정하지 않습니다. 대신 뷰포트의 초기 컨테이닝 블록을 기준으로 삼아 배치합니다. 단, 요소의 조상 중 하나가 `transform`, `perspective`, `filter` 속성 중 어느 하나라도 `none`이 아니라면 뷰포트 대신 그 조상을 컨테이닝 블록으로 삼습니다. (`perspective`와 `filter`의 경우 브라우저별로 결과가 다름에 유의) 최종 위치는 `top`, `right`, `bottom`, `left` 값이 지정합니다.

이 값은 항상 새로운 쌓임 맥락을 생성합니다. 문서를 인쇄할 때는 해당 요소가 모든 페이지의 같은 위치에 출력됩니다.

### sticky

요소를 일반적인 문서 흐름에 따라 배치하고, 테이블 관련 요소를 포함해 가장 가까운 스크롤 되는 조상과 표 관련 요소를 포함한 컨테이닝 블록 (가장 가까운 블록 레벨 조상) 을 기준으로 `top`, `right`, `bottom`, `left`의 값에 따라 offset을 적용합니다. Offset은 다른 요소에는 영향을 주지 않습니다.

이 값은 항상 새로운 쌓임 맥락을 생성합니다. Sticky 요소는 스크롤이 가능한 조상 요소가 아닌 "scrolling mechanism" (`overflow`가 `hidden`, `scroll`, `auto`, 또는 `overlay`) 이 지정된 가장 가까운 조상 요소를 기준으로 합니다.

<br>

## Reference

https://developer.mozilla.org/ko/docs/Web/CSS/position
