# 소개

> NestJS는 효율적이고 확장 가능한 Node.js 서버 측 애플리케이션을 구축하기 위한 프레임워크 입니다. JavaScript를 사용하며 TypeScript로 구축되고 완벽하게 지원합니다.

본질적으로 NestJS 는 서버 측 애플리케이션을 빠르고 쉽게 작성하는 데 도움이 되는 강력한 메서드와 구현을 포함하는 Node 상단의 레이어입니다.  
NestJS는 당신의 모든 요구 사항을 충족하는 데 매우 편리합니다. 사용자 정의에 유연하며, 기본적으로 Express를 사용하지만 선택적으로 Fastify도 사용하도록 구성할 수 있습니다.

# Node.js 가 있는데 왜 NestJS가 필요한가?

- Nest는 Node 위에 추상화 계층을 제공합니다. 즉, Node의 기능을 활용할 수 있을 뿐만 아니라 더 나은 성능과 효율을 위해 더 강력한 API들을 드러냅니다.

- 개발자들은 여러 기능을 좋아합니다. Nest에 개발 프로세스를 가속할 수 있는 수많은 모듈들이 있습니다.

- 백엔드 개발 패러다임과 더불어 견고함을 갖췄습니다.

- NestJS는 데이터베이스 작업에 사용할 수 있는 ORM으로 수준 높은 구성을할 수 있습니다. 이것은 다시 Active Record 및 Data Mapper패턴과 같은 훌륭한 TypeORM 기능을 가지고 있으며 쉽게 활용할 수 있습니다.

- Nest의 아키텍쳐는 Angular 프레임워크에서 크게 영감을 받아 만들어졌습니다. 쉽게 테스트할 수 있고 코드베이스를 효율적으로 유지할 수 있습니다.
  <br/>
  <br/>

## TypeORM에서 Active Record 와 Data Mapper 패턴이 무엇인가?

---

### Active Record 패턴

모델 자체 내에서 모든 쿼리 메서드를 정의하고 모델 메서드를 사용하여 개체를 저장, 제거 및 로드합니다. 간단히 말해서 Active Record 패터ㅏㄴ은 모델 내에서 데이터베이스에 엑세스하는 접근 방식입니다.

```js
import { BaseEntity, Entity, PrimaryGeneratedColumn, Column } from 'typeorm';

@Entity()
export class User extends BaseEntity {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  firstName: string;

  @Column()
  lastName: string;

  @Column()
  isActive: boolean;
}
```

모든 Active Record Entity는 Entity 작업을 위한 메서드를 제공하는 BaseEntity 클래스를 extends 해야합니다.
아래는 이런 Entity로 작업하는 방법의 예입니다:

```js
// example how to save AR entity
const user = new User();
user.firstName = 'Timber';
user.lastName = 'Saw';
user.isActive = true;
await user.save();

// example how to remove AR entity
await user.remove();

// example how to load AR entities
const users = await User.find({ skip: 2, take: 5 });
const newUsers = await User.find({ isActive: true });
const timber = await User.findOne({ firstName: 'Timber', lastName: 'Saw' });
```

BaseEntity에는 표준 Repository의 대부분의 메서드가 있습니다.
아래는 이름과 성을 기준으로 사용자를 반환하는 함수를 생성한다고 가정하고, 다음과 같은 함수를 User클래스의 정적 매서드로 만들 수 있습니다.

```js
import { BaseEntity, Entity, PrimaryGeneratedColumn, Column } from 'typeorm';

@Entity()
export class User extends BaseEntity {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  firstName: string;

  @Column()
  lastName: string;

  @Column()
  isActive: boolean;

  static findByName(firstName: string, lastName: string) {
    return this.createQueryBuilder('user')
      .where('user.firstName = :firstName', { firstName })
      .andWhere('user.lastName = :lastName', { lastName })
      .getMany();
  }
}

// 아래처럼 사용자 정의 메서드를 사용할 수 있습니다.
const timber = await User.findByName('Timber', 'Saw');
```

### Data Mapper 패턴

Data Mapper 패턴을 사용하며 repositories 라는 별도의 클래스에 모든 쿼드 메서드를 정의하고 개체를 저장, 제거 및 로드 합니다. Data Mapper에서 Entity는 단지 속성을 정의하고 일부 "더미" 메서드를 가질 수 있습니다.
간단히 말해서 Data Mapper는 Model 대신 Repository 내의 데이터베이스에 엑세스하는 접근방식입니다.

```js
import { Entity, PrimaryGeneratedColumn, Column } from 'typeorm';

@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  firstName: string;

  @Column()
  lastName: string;

  @Column()
  isActive: boolean;
}
```

이러한 Entity로 작업하는 방법:

```js
const userRepository = connection.getRepository(User);

// example how to save DM entity
const user = new User();
user.firstName = 'Timber';
user.lastName = 'Saw';
user.isActive = true;
await userRepository.save(user);

// example how to remove DM entity
await userRepository.remove(user);

// example how to load DM entities
const users = await userRepository.find({ skip: 2, take: 5 });
const newUsers = await userRepository.find({ isActive: true });
const timber = await userRepository.findOne({
  firstName: 'Timber',
  lastName: 'Saw',
});
```

이제 이름과 성을 기준으로 사용자를 반환하는 함수를 생성한다고 가정하면

```js
import { EntityRepository, Repository } from 'typeorm';
import { User } from '../entity/User';

@EntityRepository()
export class UserRepository extends Repository<User> {
  findByName(firstName: string, lastName: string) {
    return this.createQueryBuilder('user')
      .where('user.firstName = :firstName', { firstName })
      .andWhere('user.lastName = :lastName', { lastName })
      .getMany();
  }
}

// 아래처럼 사용자 정의 메서드를 사용할 수 있습니다.
const userRepository = connection.getCustomRepository(UserRepository);
const timber = await userRepository.findByName('Timber', 'Saw');
```

### 그래서 둘 중 무엇을 선택하느냐 ?

두 가지 패턴 모두 장단점이 있습니다.
Data Mapper 패턴은 유지관리에 도움이 되며, 큰 앱에서 효율적입니다.
Active Record 접근 방식은 작은 앱에서 작업을 단순하게 유지하는데 도움이 됩니다. 그리고 단순성은 항상 더 나은 유지 관리의 핵심입니다.

# NestJS는 3-Tier 아키텍쳐를 따릅니다.

아키텍쳐를 구축할 때 가장 중요한 것은 함께 의미가 있는 부분은 함께 존재하도록 애플리케이션을 분리하는 것입니다.  
아래 흐름도에서 컨트롤러와 서비스 계층이 함께 작동하여 로직을 수행하지만 완전히 다른 두 가지라는 것을 알 수 있습니다.  
컨트롤러는 기본적으로 애플리케이션의 경로를 처리합니다. 컨트롤러에는 몇 가지 다른 경로가 있을 수 있으며, 모든 요청을 수신하는 컨트롤러를 제어하는 라우팅 메커니즘에 따라 다릅니다. 물론 컨트롤러 안에서도 모든 비지니스 로직을 수행할 수 있습니다. 그러나 애플리케이션이 성장하고 더 많은 컨트롤러와 경로를 등록하게 되고 더 많은 비지니스 로직을 작성하게 된다면 애플리케이션은 상황을 통제할 수 없게 되고 유지 관리도 할 수 없게 됩니다.

<img src="https://miro.medium.com/max/1400/1*iO7R3erL7nquWs6vfm3cMA.png" width="80%"></img>

```
1. Controllers: 컨트롤러는 애플리케이션에 대한 요청을 수신하고 경로를 처리합니다.

2. Service Layer: 비지니스 로직을 작성합니다. 예를 들어, 데이터 생성, 저장 및 업데이트 방법을 결정합니다.

3. Data Access Layer: 데이터베이스에 저장된 데이터에 엑세스하기 위한 논리를 관리하고 제공합니다.
```

# NestJS 의 폴더 구조

CLI를 사용하여 새 NestJS 프로젝트를 시작하면 몇 가지 기본적인 핵심 파일들을 제공합니다. 디렉토리 구조는 아래와 같습니다.

```
src
| — app.controller.spec.ts
| — app.controller.ts
| — app.module.ts
| — app.service.ts
| — main.ts

app.controller.ts: 모든 애플리케이션 경로를 포함할 컨트롤러 파일입니다.

app.controller.spec.ts: 컨트롤러에 대한 유닛 테스트 파일입니다.

app.module.ts: 모듈은 기본적으로 응용 프로그램의 모든 컨트롤러와 공급자를 묶습니다.

app.service.ts: 특정 작업을 수행하는 메서드가 포함됩니다.

main.ts: 모든 모듈들을 가져오고 Nest에서 제공하는 NestFactory를 사용하여 앱 인스턴스를 생성합니다.
```

NestJS 서버를 애플리케이션을 구축할 때 따라야하는 구조입니다. 이것은 깨끗하고 확장 가능하며 유지 관리 가능한 코드를 작성하는 데 도움이 되는 것입니다.
