# Providers

공급자는 NestJS의 기본 개념입니다. services, repositories, factories, helpers등 공급자등이 있고, 공급자의 주요 아이디어는 종속성으로 주입될 수 있다는 것입니다.

## 미리 알기

- 계층형 구조(Layered Architecture)
- 제어 역전(IoC, Inversion of Control)
- 의존성 주입(Dependency Injection)

<img src="https://docs.nestjs.com/assets/Components_1.png" with="80%">

## Services

```js
import { Injectable } from '@nestjs/common';
import { Cat } from './interfaces/cat.interface';

@Injectable()
export class CatsService {
  private readonly cats: Cat[] = [];

  create(cat: Cat) {
    this.cats.push(cat);
  }

  findAll(): Cat[] {
    return this.cats;
  }
}
```

```js
export interface Cat {
  name: string;
  age: number;
  breed: string;
}
```

이 서비스는 데이터 저장 및 검색을 담당하며 CatsController에서 사용하도록 설계되었습니다.  
@Injectable() 데코레이터는 CatsService가 Nest IoC 컨테이너에서 관리할 수 있는 클래스임을 선언하는 메타데이터를 첨부합니다.

---

고양이를 검색하는 서비스를 컨트롤러에서 사용하는 방법입니다.
CatsService를 import 하고 Controller에 주입합니다.

```js
import { Controller, Get, Post, Body } from '@nestjs/common';
import { CreateCatDto } from './dto/create-cat.dto';
import { CatsService } from './cats.service';
import { Cat } from './interfaces/cat.interface';

@Controller('cats')
export class CatsController {
  constructor(private catsService: CatsService) {}

  @Post()
  async create(@Body() createCatDto: CreateCatDto) {
    this.catsService.create(createCatDto);
  }

  @Get()
  async findAll(): Promise<Cat[]> {
    return this.catsService.findAll();
  }
}
```

CatsService는 클래스의 constructor를 통해 주입됩니다.

```js
class CatController {
  constructor(private catsService: CatsService) {}
}
위 코드는 아래 코드와 같은 표현입니다.
class CatController {
  private catsService: CatsService
  constructor(CatsService: CatsService) {
    this.catsService = CatsService;
  }
}
```

---

## Dependency injection
