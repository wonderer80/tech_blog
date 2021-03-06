---
title: 초보를 위한 테스트 코드 없이 TDD 배우기
date: 2018-04-04 16:31:35
tags:
- TDD
---
이전 포스팅을 통해서 TDD를 배우기 왜 어려운지에 대한 이야기를 하였다. 그럼 TDD를 위한 기반지식이 없는 상태에서 TDD를 배우려면 어떻게 해야할까? 당연하겠지만 정석은 차근차근 필요한 지식들을 공부해나가는 것이다. 테스트 하는 법, 테스트 코드를 작성하는 법, 리팩토링을 하나씩 수련해나가는 것이다. 하지만 하나하나가 만만치가 않다. 나는 언제야 TDD를 해볼 수 있는 것일까?

그래서 이번 포스팅에서는 정석적인 학습법이 아닌 내가 시도한 사파의 수련법을 공유해보고자 한다. 이로인한 주화입마는 책임지지 않으니 판단은 각자에게 맡기도록 하겠다.

내가 처음 TDD를 접했을 때 당시에는 테스트 코드가 뭔지에 대한 개념이 전혀 없는 상태였다. 리팩토링에 대해서는 책을 조금 보고 조금씩 실무에 적용해보는 단계였다. 사실 테스트 코드가 뭔지도 모르는 상황에서 TDDBE를 봤을 때 나는 그게 정확하게 무엇을 하는지 이해하지는 못했지만, 딱 한가지 감탄한 접근 방법이 있었다.

일반적으로 개발을 할 때는 객체를 설계하고 그 기능들을 완성한 다음에 그 기능을 활용하여 개발을 해왔다. 완성된 하나의 서비스나 기능을 만들 때 그것을 위한 세부 기능 파트를 만들고 마지막에 그것들을 조합해서 완성하는 것이다. 그런데 TDD 에서는 그런 생각을 완전히 뒤집는다. 처음부터 완성되었다고 가정하고 개발을 하기 시작하는 것이었다. 메서드를 호출하는데 메서드 정의조차 하지 않고 메서드 호출을 한다. 그렇게 하면 당연히 에러가 나는데 거기에서부터 점진적인 개발이 시작된다.

이런 접근 방법에 감탄한 이유가 몇가지 있었는데 다음과 같다.

# TDD 초보자가 얻은 작은 통찰
## 인터페이스 설계가 명확하고 간결하게 된다

파트 단위로 먼저 개발을 하다보면 지나치게 범용적인 설계를 하거나 실제 호출되는 상황을 고려하지 못한 설계가 되는 경우가 많다. 그로인해 결과적으로 지나치게 많은 시간을 소비하거나 과도한 기능 설계나 잘못된 설계로 변경이 필요하게 되어 재작업하는 경우가 잦다.

## 점진적인 개발을 하기 편하다

기존의 방식을 완성된 기능을 만들기 위해 그것을 위한 세부 기능들이 완성이 되기전까지 확인할 수 있는 것들이 매우 제한적이다. 하지만 사용하는 곳부터 구현하다보면 매우 빠른 지점부터 내가 확인할 수 있는 것들이 생긴다.

# 테스트 코드 없이 TDD 활용하기

그럼 이제 본론으로 들어가서 기반지식이 별로 없는 상황에서의 TDD를 활용하는 방법에 대해서 이야기해보자. 이야기했듯이 나는 TDD를 처음 접했을 때 테스트 코드가 뭔지도 몰랐다. 테스트 코드가 뭔지도 모르고 만들줄도 모르는데 TDD가 역설적으로 들릴 수 도 있다. 하지만 테스트 코드를 만들 줄 모른다고 해서 테스트를 못하는 것은 아니다. 테스트에 대한 이야기도 나중에 좀 더 자세히 다를 수도 있겠지만 우선 그 부분은 생략하고 필자가 테스트 코드 없이 TDD를 활용한 방법을 소개해보겠다.

![Sequence diagram](/images/UML_Sequence_diagram.jpg "어떤 방식으로 개발하느냐에 따라 구현 순서가 다르다")

## 가장 끝단에서부터 개발하기

위에서도 이야기 했지만 TDD의 특징 중 하나가 기능구현 이전 단계 부터 사용하는 부분을 개발하는 것이다. 본래 TDD에서는 테스트 코드가 수행되는 부분에서 그 로직을 호출하게 되지만 나는 그냥 메인 코드에서 그렇게 작업을 하기 시작했다. 이런 접근법은 매우 간단하게 개발을 시작하게 만들어주었고 나에게 빠르게 결과를 확인할 수 있게 해주었다.

위 다이어그램의 기능을 내가 구현한다고 생각해보자. 일반적인 방식에서는 Customer, Waiter, Chef 의 기능을 모두 설계, 개발 후에 최종적으로 해당 기능들을 조합해서 만들게 될 것이다. 하지만 TDD 방식으로 개발한다면 메인 로직에서 Customer의 order를 호출하는 부분(다이어그램상으로는 정의되어 있지 않다)부터 개발하게 된다.

## 미구현 부분을 Mock으로 처리하고 점진적으로 구현하기

끝단부터 개발해서 역으로 채워나가는 과정에서 빠르게 결과를 확인하기 위해서 단계별로 기능을 구현하고 미구현 부분은 하드코딩을 통해서 Mock으로 처리한다.

메인 로직에서 Customer의 order 메서드로 기능이 시작된다고 가정했을 때 Waiter의 order, Chef의 orderfood 라는 메서드가 내부적으로 호출된다. 처음에 메인로직에서 Customer의 order를 호출하고 Customer의 order를 만들 때에는 Customer order의 전체를 Mock 처리한다. 여기서의 Mock 처리는 보통은 하드코딩을 통해서 의도한 결과를 리턴하는 것이다. 이 과정에서 Customer 객체의 정의나 order 메서드의 인터페이스가 정의 되는데, 이 과정을 통해 order라는 기능이 전체적인 프로그램에서의 흐름을 확인할 수 있게 되면 다음엔 Waiter의 order를 호출하는 로직을 추가한다.

이 후의 과정은 앞서 했던 과정의 반복으로 이루어지는데, 중간 단계를 섣불리 생략하면 무한 재귀함수에 빠진 것처럼 뇌에서 stackoverflow 가 발생될 것이다. 초기 수련 단계에서는 반드시 모든 단계를 생력하지 않고 순차적으로 진행하는 것이 중요하다.

# TDD를 통해 무엇을 배울 것인가?

위에서 내가 접근한 방법을 보고 어떤 생각이 드는가? '에이~ 이게 무슨 TDD야' 라는 생각이 들지도 모르겠다. 저렇게 하는 것만을 가지고 '난 TDD 한다'라고 말하기에는 분명 부족함이 많다.

그런데 여러분이 TDD를 하려고 하는 이유는 무엇인가?  내가 다른 사람들에게 TDD를 할 줄 안다고 말하는 것이 중요하다면 이런 접근은 별로 의미가 없을지도 모르겠다. 하지만 엔지니어로서 새로운 기술을 습득하고 역량을 발전시키는데 목적이 있다면 필자는 이런 접근 방법도 괜찮다고 생각한다.

{% blockquote%}
TDD의 아이러니 중 하나는 TDD가 테스트 기술이 아니라는 점이다(워드 커닝엄의 선문답이다). TDD는 분석 기술이며, 설계 기술이기도 하다. 사실은 개발의 모든 활동을 구조화하는 기술이다.

_<켄트 백, 테스트 주도 개발>_
{% endblockquote %}

이러한 접근 방식을 내가 추천하는 이유는 TDD에서의 점진적인 개발과도 맞닿아 있다고 생각하는데, 모든 걸 완벽하게 이해하고 완성해야만 그 결실을 얻을 수 있는게 아니라 내가 이해하고 있는 수준 안에서 실천하고 그 안에서의 이점들을 취할 때 TDD를 지속할 수 있는 힘이 된다고 생각한다.

마지막으로 혹시라도 이 포스팅만을 보고 TDD에 대해 이해했다고 착각하는 실수를 범하지 않았으면 좋겠다. 이 방식은 앞서 말했다시피 필자가 밟아온 시행착오 중의 하나일 뿐이며, 단순히 TDD를 글로만 이해하는데에는 한계가 있다. 여러분의 실제 개발 환경에서 직접 시도하는 과정에서만 TDD에 대해 배우고 이해할 수 있다.

이 글을 통해 다른 많은 분들의 경험담을 전해들을 수 있었으면 좋겠다.
