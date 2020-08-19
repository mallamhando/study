# Rust
이미 Rust 를 embeded system 에 적용한 예제는 넘쳐나고 있다.

Rust 를 linux kernel 에 넣는것이 검토될 정도이다.

Rust 가 현재 가지고 있는 한계는 극복해야 할 과제이지 Rust 를 쓰지 말아야 할 이유는 아니다.

embeded system 엔지니어에게 Rust 는 더이상 선택 사항이 아니게 되었다.

많은 embeded system 관련 신기술들이 곧 Rust 로 작성되서 나타날 것이기 때문이다.

## Rust 의 장점
Rust 에 성능을 높이기 위한 마법 기능이 있는 것이 아니다.

하지만 Rust 는 C 로 최적화된 네이티브 코드와 비슷한 성능으로 동작할 수 있다.

이유는 Rust 에 특별한 기능을 더한 것이 아니라 C 이후 프로그래밍 언어들에 추가된 기능들을 오히려 제거했기 때문이다.

Rust 는 리소스에 대한 소유권을 명시하고 컴파일 단계에서 오류를 찾아내는 방식를 사용하여,

기존 언어들이 GC 를 사용하면서 발생하는 비효율을 제거하고 안정성과 속도를 얻었다.

## https://rinthel.github.io/rust-lang-book-ko/ 를 읽고
독해력이 부족한 나를 위해 친절하게 번역해 놓지는 않았다.
후반부에 이르러서야 영어로 읽는게 더 편하다는 생각이 들었고 시간이 아까웠다.

C 와 Javascript 를 생각하면서 읽었는데 C++ 이 나온다.
왜 Javascript 를 dumb 하게 두자는 움직임이 있는지 알것 같다.
동작을 구현하는데 Javascript 대비하여 더 단순하고 직관적이지 않다.

대충읽었다. 제목만 읽는 경우도 많았다. 어차피 다 까먹을거라 단순히 단어들만 머리속에서 익숙해지는게 더 나아보였다.
또한 다른 언어에 존재하는 기능을 다시 배우는 일은 지루하다. 동시성 처리 부분에 와서야 차이가 느껴진다.

## Kernel 에 Rust 가 필요한 이유 @ 2019
대부분의 프로그램 문제들이 메모리에서 발생,
하지만 기존 메모리 관리형 프로그래밍 언어를 사용하면 overhead 가 발생하고
시스템의 error handling 시스템과 충돌을 일으킨다.

메모리 관리를 컴파일 과정에서만 수행하는 Rust 가 c 의 한계를 극복하게 해준다.

* https://ldpreload.com/p/kernel-modules-in-rust-lssna2019.pdf

## 참고자료
https://tourofrust.com/00_ko.html

http://hacks.mozilla.or.kr/2015/06/diving-into-rust-for-the-first-time/

https://rust-kr.org/

https://rinthel.github.io/rust-lang-book-ko/

https://github.com/phil-opp/blog_os

#### Embeded Rust
https://rust-embedded.github.io/book/

#### Rust 커널은 불가능?
https://www.reddit.com/r/rust/comments/6f142p/rewrite_the_linux_kernel_in_rust/

#### Rust 커널은 가능?
https://www.phoronix.com/scan.php?page=news_item&px=Torvalds-Rust-Kernel-K-Build
