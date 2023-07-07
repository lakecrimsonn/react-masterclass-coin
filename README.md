# 5.0 setup

- npm i react-router-dom@5.3.0 react-query
- npm i --save-dev @types/react-router-dom
- api 서버 CoinPaprika
- https://api.coinpaprika.com/#tag/Tickers

---

# 5.1 styles

- 스타일 컴포넌트는 css설정을 컴포넌트 하나에 고정시킨다. 그래서 도큐먼트 전체에 적용하려면 styled-component의 createGlobalStyle를 이용해야 한다.
- html 도큐먼트의 디폴트 css를 수정하는 방법은 ResetCSS를 사용하면 된다. a태그는 디폴트로 언더라인이 깔리지만, 언더라인이 없도록 변경할 수 있게 된다. `npm -i styled-reset`
- ResetCSS는 너무 간단한 파일 하나라서 그냥 복사해서 사용할 수 있다. createGlobalStyle안에 css를 복사하면 된다.
- 컴포넌트를 리턴시킬 때는 하나의 요소만 리턴할 수 있다. 그래서 보통 div로 감싸는 편인데, 이러면 쓸데 없는 div가 많이 늘어나게 된다. `div`대신 고스트 컴포넌트인 빈태그를 이용하면 된다.`<>`

  ```typescript
  <><GlobalStyle/><Router/><>
  ```

- 구글폰트에서 새로운 폰트를 @Import로 가져다 쓰자. 임포트를 해야 새로운 폰트패밀리를 사용할 수 있다.

- [Reset CSS](https://github.com/zacanger/styled-reset/blob/master/src/index.ts)
- [google font](https://fonts.google.com)
- [Flat UI Color](https://flatuicolors.com/palette/gb)
- [createGlobalStyle](https://styled-components.com/docs/api#createglobalstyle)

---

# 5.2 Home part One

- a태그를 이용한 리스트를 만들면 페이지가 새로고침이 되어버린다. 리액트라우터돔의 `Link`를 이용하면 새로고침 없이 페이지를 이동할 수 있다. 링크는 사실상 a태그를 만드는 건데 리액트라우터돔이 새로고침이 되지 않게 도와준다. 그래서 hover 기능을 추가할 때, a태그의 css를 설정하는 스테이트먼트를 추가해줘야 한다.

- a태그 텍스트의 색상은 디폴트로 파란색이지만 기본 텍스트 색상과 동일하게 만들 수 있다. App컴포넌트에서 a태그의 color를 `inherit`으로 설정하면 된다. 부모와 같은 색상을 사용하겠다는 뜻이다.
- a태그의 영역은 패딩이 늘어난 리스트의 크기와 동일하지 않다. 그래서 마우스를 리스트 위에 올려보면 마우스 화살표가 손가락으로 변경되지 않는다. a태그의 display를 `block`으로 설정해주자. 리스트의 오른쪽 화면 끝까지 블록으로 설정된다. `padding` 역시 추가해줘서 선택영역을 늘리자.
- a태그의 `transition`을 color 0.2s ease-in으로 설정해줘서 디테일을 살려보자. 마우스를 호버했을 때 색상이 변경이 되는 것 같아서, a:hover 스테이트먼트 안에서 해결하는 듯 보이지만 아니다. 호버가 아니라 a태그에 직접 transition을 적용하자.
- 컨테이너 컴포넌트에 `margin` 0 auto; `max-width`를 추가하자. 화면이 커져도 가운데에 리스트가 고정이 되어 있다.

---

# 5.3 Home part Two

- api를 가져올 때 fetch보다 axios가 더 편하다. fetch는 json으로 변환해줘야 하지만, axios은 기본적으로 json으로 가져온다.
- ```javascript
  const getCoins = async () => {
    const res = await axios("https://api.coinpaprika.com/v1/coins");
    setCoins(res.data.slice(0, 100));
    setLoading(false);
  };
  ```
- api에서 오는 데이터 역시 어떤 타입인지 설명해주는 인터페이스가 있어야 한다.
- array에 대한 인터페이스를 적용할 때는 `useState<CoinInterface[]>([]);` 이렇게 사용한다.
- `()()` 함수는 바로 실행할 수 있는 함수인데, 일반적인 함수의 모형을 생각하면 어렵지 않게 이해할 수 있다. useState() -> (useState)() 이렇게 말이다.

- ```javascript
    useState(() => {}, []); // useState는 이렇게 생겼다
    useState((=>{
      ()() // 함수를 따로 만들지 않고 바로 실행할 수 있는 함수
    },[]));
    useState((=>{
      (async ()=>{
        const response = await fectch('https://url');
        const json = await response.json();
        setCoins(json.slice(0,100)); // 100개 짤라오기
      })();
    },[]));
  ```

---

# 5.4 Router States

- [Crpyto Icon API](https://coinicons-api.vercel.app/)
- `` <img src={`https://cryptocurrencyliveprices.com/img/${coin.id}.png`}/> ``
- `https://cryptocurrencyliveprices.com/img/${coin.id}.png`
- `https://static.coinpaprika.com/coin/${coin.id}/logo.png`
- a태그의 display가 flex면 block 속성을 제거해야 텍스트가 가운데 정렬을 한다.
- 링크태그를 이용하면 새로고침을 하지 않고 페이지에 데이터를 보내고 받을 수 있다. 브라우저에 이미 있는 데이터를 넘겨주면 된다. 다시 api 호출을 통해서 데이터를 가져오지 말자.
- 링크태그를 이용해서 스테이트를 url이 아닌 비하인드씬으로 보낸다.

- ```javascript
  <Link to={`/${c.id}`}> # 기존
  <Link to={{
    pathname: `${c.id}`,
    state: {
      name: c.name
    }
  }}>
  ```
- 스테이트를 수신할 때는 `useLocation`를 통해서 받아야 한다.
- 스테이트를 비하인드 더 씬으로 주고 받을 경우 문제는 무조건 스테이트를 보낸 곳에서 페이지를 시작해야 한다. 페이지에 접속할 때, 꼭 홈을 거치고 페이지를 열어야 한다. url를 통해서 바로 다이렉트로 접근하면 스테이트를 받아올 수 없어서 오류가 난다.
- 오류페이지를 방지하는 방법: `{state?.name || "not available"}`
- state가 존재하면 name을 불러와줘. state가 존재하지 않으면 error를 던지지 말고 undefined를 보여줘.
- state?.name이 null, undefined, empty string이면 "not available"을 보여줘

---

# 5.5 Coin Data

- [coin api](https://api.coinpaprika.com/v1/coins/btc-bitcoin)
- [coin ticker api](https://api.coinpaprika.com/v1/tickers/btc-bitcoin)

---

# 5.6 Data Types

- api로 받아온 json의 타입 편리하게 정해주는 방법

1. 브라우저 콘솔에서 json을 출력한다.
2. 오른쪽 마우스를 클릭하고 `store object as global variable`클릭
3. 콘솔창 프롬프트에 `Object.keys(temp1).join()` 입력. 하나의 문자열로 만들고 복사해서 가져온다.
4. ctrl+d 연타해서 쉼표 전부다 지우고 엔터.
5. `Object.values(temp1).map(c=>typeof(c)).join()` 밸류 가져오기
6. ctrl+shift+L 혹은 ctrl+alt로 커서 길게 만든 다음 end버튼으로 커서를 문자 끝으로 보내주고 밸류 잘라 붙여넣어주기.

- 가져온 타입이 배열인 경우
- 따로 인터페이스를 만들어주고 타입을 지정해준다.
- `tags: ITags[];` 대괄호를 잊지 말아야한다.
- json안에 배열도 있지만, 변수안에 다른 딕셔너리가 있다. `quotes.USD`

---

# 5.7 Nested Routes part One

- [Nested React Router V6 Guide](https://ui.dev/react-router-nested-routes)
- useEffect를 이용하는 경우 보통 마지막 [] 어레이어 변수를 넣어준다. 웹페이지의 성능을 때문에 변수의 스테이트가 변경할 때만 리렌더링 하도록 설정하기 위해서다. 하지만 이번 케이스는 항상 url에 변수를 받아오기 때문에 변수의 스테이트가 변경되지 않으니, 어레이를 비워두어도 괜찮다. 어레이가 비어 있는 걸 no dependency라고 한다.

- ```typescript
  <Title>
    {state?.name ? state.name : loading ? "Loading..." : info?.name}
  </Title>
  ```

- state가 존재하면 name을 보여줘. 존재하지 않으면 undefined를 보여줘. state가 존재하고, name에 값이 존재한다면, state.name을 보여줘.
- state.name이 없다면 loading을 보여줘. loading이 true면 "Loading..."를 보여줘. loading이 false면 info?.name을 보여줘. info가 존재하면 name을 보여줘. info가 없으면 undefined를 보여줘.
- `NestedRoute`: 라우트안에 있는 라우트. 다른 도메인으로 요청을 보냈지만 화면이 변경되지 않고, 같은 화면에서 응답하는 방법.
- App 컴포넌트는 Router 컴포넌트를 이미 포함하고 있다. Router 컴포넌트는 Coin 컴포넌트를 포함하고 있다. Coin 컴포넌트에 새로운 Route태그를 가지게 된다. 한마디로 라우트안에 라우트.

---

# 5.8 Nested Routes part Two

- 네스티트 라우트를 이용하기 때문에 URL을 통해서 화면의 일정 부분이 리렌더링이 된다. URL을 이용하는 태그는 anchor인데, 리액트를 이용하니 Link태그를 이용해서 화면을 리렌더링하게 된다.
- `useRouteMatch`은 특정한 URL를 선택하면 오브젝트를 받아 오는 후크다.
- `const chartMatch = useRouteMatch("/:coinId/chart");` /:coinId/chart라는 텍스트가 URL에 존재하면 링크태그에 있는 오브젝트를 보여준다. useParams는 이전 페이지의 링크태그에서 보내주는 pathname를 통해 URL에 있는 텍스트를 받아온다. useLocation은 이전 페이지의 스테이트를 받아와서 리프레쉬를 막아주면서 UX을 지켜준다.
- ```typescript
  const Tab = styled.span<{ isActive: boolean }>`
    text-align: center;
    text-transform: uppercase;
    font-size: 12px;
    font-weight: 400;
    background-color: rgba(0, 0, 0, 0.5);
    padding: 7px 0px;
    border-radius: 10px;
    color: ${(props) =>
      props.isActive ? props.theme.accentColor : props.theme.textColor};
    a {
      display: block;
    }
  `;

  <Tab isActive={priceMatch !== null}>
    <Link to={`/${coinId}/price`}>Price Detail</Link>
  </Tab>;
  ```

- isActive의 boolean을 통해서 태그를 활성화할 수 있다. priceMatch(useRouteMatch 후크의 변수)에 오브젝트가 존재해서 true를 반환하고 탭이 활성화된다. useRouteMatch를 이용하지 않으면 null을 반환한다.

---
