# Deploying Rust in Existing Firmware Codebases
- https://security.googleblog.com/2024/09/deploying-rust-in-existing-firmware.html

Android's use of safe-by-design principles drives our adoption of memory-safe languages like Rust, making exploitation of the OS increasingly difficult with every release.
To provide a secure foundation, we’re extending hardening and the use of memory-safe languages to low-level firmware (including in Trusty apps).

안드로이드는 safe-by-design 원칙을 사용하고 있다.
이 원칙은 우리를 memory-safe 한 Rust 같은 언어를 채택하게 하고 있고,
매 release 마다 OS 의 개발의 어려움을 증가시키고 있다.
안전한 토대를 제공하기 위해, 우리는 견고함을 확장하고 있다. 그리고 memory-safe 한 언어를 Trusty app 들을 포함해 low-level 펌웨어까지 사용하고 있다.

In this blog post, we'll show you how to gradually introduce Rust into your existing firmware, prioritizing new code and the most security-critical code.
You'll see how easy it is to boost security with drop-in Rust replacements, and we'll even demonstrate how the Rust toolchain can handle specialized bare-metal targets.

이 포스팅에서 우리는 가장 보안 중심의 코드를 우선적으로 하는 Rust 코들를 기존 펌웨어에 어떻게 서서히 적용할수 있는지를 보여줄 것이다.

Drop-in Rust replacements for C code are not a novel idea and have been used in other cases, such as librsvg’s adoption of Rust which involved replacing C functions with Rust functions in-place.
We seek to demonstrate that this approach is viable for firmware, providing a path to memory-safety in an efficient and effective manner.

C 를 Rust 로 변환하는 것은 새로운 생각이 아니고, librsvg 처럼 여러 경우에 이미 사용되어 왔다.
효과적이고 영향력있게 memory-safety 의 길로 가는 방법을 제공하기 위해,
우리는 이러한 접근이 펌웨어도 가능하다는 것을 보여주려고 한다.

## Memory Safety for Firmware

## Incremental Rust Adoption

## Challenges and Considerations
### The Rust Standard Library and Bare-Metal Environments
### Choosing a Component to Replace
### Choosing a Pre-Existing Crate (Rust Library)
### Porting a std Library to no_std

## Custom Target Architectures
### Building Rust With LLVM Prebuilts

## Creating a Drop-In Rust Shim
### Exposing the Same API
### Calling Back to C/C++ Code
### Hooking Up The Firmware’s Bare-Metal Environment
### Fallible Allocations and alloc
### Build Optimizations

## Memory Safety for Firmware, Today
Using the process outlined in this blog post, You can begin to introduce Rust into large legacy firmware code bases immediately.
Replacing security critical components with off-the-shelf open-source memory-safe implementations and developing new features in a memory safe language
will lead to fewer critical vulnerabilities while also providing an improved developer experience.

이 포스팅에서 요약된 방법을 이용해 Rust 를 거대한 기존 펌웨어 소스에 바로 적용할수 있다.
보안 중심의 컴포넌트를 규격화된 오픈 소스 memory-safe 구현으로 교체하고, memory-safe 한 언오로 새로운 기능을 개발함하는 것은
개선된 개발 경험을 제공하면서 동시에 낮은 중요 취약성의 상태로 갈수 있게 해준다.
