# Controller

컨트롤러는 들어오는 요청을 처리하고 클라이언트에 응답을 반환하는 역할을 합니다.

<br/>
<img src="https://docs.nestjs.com/assets/Controllers_1.png" width="80%"></img>

<br/>
컨트롤러의 목적은 애플리케이션에 대한 특정 요청을 수신하고, 라우팅 메커니즘은 어떤 컨트롤러가 어떤 요청을 수신하는지 제어합니다. 각 컨트롤러에는 한 가지 이상의 경로가 있으며 다른 경로는 다른 작업을 수행할 수 있습니다. Nest는 기본 컨트롤러를 만들기 위해 클래스와 데코레이터를 사용합니다. 데코레이터는 클래스를 필수 메타데이터와 연결하고 Nest가 라우팅 맵을 생성할 수 있도록 합니다.(요청을 해당 컨트롤러에 연결함).

<br/>

## Routing

```js
import { Controller, Get } from '@nestjs/common';

/*
컨트롤러 데코레이터를 통해 관련 경로를 그룹화하고 반복적인 코드를 최소화할 수 있습니다.
데코레이터는 클래스 선언, 메서드, 접근자, 프로퍼티 또는 매개 변수에 첨부할 수 있는 특수한 종류의 선언입니다.
아직 자바스크립트에서는 표준화 제안 2단계이며, 타입스크립트에서 실험적 기능으로 사용할 수 있습니다.
참고: https://typescript-kr.github.io/pages/decorators.html
*/
@Controller('cats')
export class CatsController {
  @Get()
  findAll(): string {
    return 'This action returns all cats';
  }
}
```

요청 경로는 컨트롤러에 선언된 경로와 http메서드와 연결하여 결정됩니다.Nest는 GET /cats 요청을 findAll() 핸들러에 매핑합니다. 그리고 /cat GET 를 엔드포인트로 요청이 들어오면 Nest는 요청을 사용자가 정의한 findAll() 메서드로 라우팅합니다. 핸들러 이름은 완전히 임의적이며 경로를 바인딩할 메서드를 선언하지만, Nest는 핸들러 이름에 아무런 의미를 부여하진 않습니다.

---

아래 핸들러는 200 상태코드와 관련 응답을 반환합니다.

```js
  findAll(): string {
    return 'This action returns all cats';
  }
```

기본적으로 Nest는 요청 핸들러가 JavaScript 객체 또는 배열을 반환할 때 자동으로 JSON으로 직렬화 후 응답합니다. 그리고
JavaScript의 원시 타입은 그 값만 보냅니다. 응답 상태코드는 200이 기본이고, POST 요청에는 201입니다. @HttpCode() 데코레이터로 응답코드를 바꿀 수도 있습니다.  
또는 라이브러리별 응답 객체를 불러와서 응답 처리 방법을 사용할 수 있습니다.  
예: Express라면 findAll(@Res() response)를 불러와 response.status(200).send()로 응답을 할 수 있습니다.  
주의 사항으로 Nest의 기본 방식이 아니라 응답 객체를 불러온다면 Nest의 기본 응답 방식이 비활성화 됩니다. 대신
@Res({ passthrough: true }) 옵션을 통해 활성화할 수 있습니다.

## Request object

핸들러에서 요청 세부 정보에 액세스하려면 @Req() 데코레이터를 추가하여 요청 개체를 삽입하도록 Nest에 지시하여 요청 개체에 액세스할 수 있습니다.

```js
import { Controller, Get, Req } from '@nestjs/common';
import { Request } from 'express';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(@Req() request: Request): string {
    return 'This action returns all cats';
  }
}
```

요청 객체는 HTTP요청을 나타내며 쿼리, 매개변수, HTTP헤더 및 본문에 대한 속성을 가지고 있습니다. Nest는 이러한 속성들을 수동으로 가져올 필요없이 전용 데코레이터를 사용해 바로 사용할 수 있습니다.
|데코레이터 | 대응되는 객체|
|---|---|
|@Request(), @Req() | req|
|@Response(), @Res() | res|
|@Next() | next|
|@Session() | req.session|
|@Param(key?: string) | req.params / req.params[key]|
|@Body(key?: string) | req.body / req.body[key]|
|@Query(key?: string) | req.query / req.query[key]|
|@Headers(name?: string) | req.headers / req.headers[name]|
|@Ip() | req.ip|
|@HostParam() | req.hosts|

> @Response(), @Res() 데코레이터를 사용할 때는 응답 객체(예: res.json(...) 또는 res.send(...))를 호출하여 일종의 응답을 발행해야 합니다. 그렇지 않으면 HTTP 서버가 중단됩니다.

## Resources
