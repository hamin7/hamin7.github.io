---
title: Bitcoin Lightweight Clients Privacy using Trusted Execution
author: Hamin
date: 2020-04-19 17:03:25
tags: [blockchain, Usenix, Security]
categories:
- [papers]
- [blockchain]

thumbnail: /gallery/BITE.png
---

이 포스트는 28th Usenix Security Symposium의 BITE: Bitcoin Lightweight Clients Privacy usin Trusted Execution을 바탕으로 작성되었습니다.

<!-- more -->

{% asset_img bitcoin.png %}
- Heavily used
- ~ 4.1 Transactions/sec
- ~ 360k Transactions/day

## Problems

- Client requirements.
  - Clients need to download and process entire chain (~ 230GB)
  - Participating in the P2P network carries high communication overhead.
  - Partial Anonymity achieved through pseudonymity.
- Using mobile clients for transaction confirmation is infeasible.
- Full reliance on the full node that stores the entire chain.
  - Light client stores only block headers, all other information is requested from the full node.
  - Fully breaks privacy.

## Strawman solutions
- Bitcoin supports Simplified Payment Verification (SPV) - Works, but sharing the addresses breaks privacy.
- Use the same approach with Bloom Filters? - Sharing the filters still breaks privacy.
- Share addresses with a TEE (SGX enclave)? - Better, but enclaves leak and privacy is still a problem.
- Send also the private key to the full node? - If enclave compromised, client looses all money.

{% asset_img Strawman.png %}

## Trusted Execution Environments

- Enable isolated execution within a user’s system.
  - Secure, integrity-protected environment.
  - Provides processing, memory, and storage capabilities.
  - Smart cards, TPM, ARM Trustzone, Keystone, etc.
  -  Intel SGX.

## Intel SGX (Software Guard Extensions).
- Intel’s architecture containing
new instructions, protective mechanisms, and key material in the CPU.
- Runtime isolation, sealing, attestation.
- Memory content encrypted

{% asset_img Intel_SGX.png}

## BITE - transaction fetching and verification
- Light client shares the addresses with the enclave on the full node. • Enclave hardened using the known techniques.
- Memory access : in-memory ORAM to prepare a response.
  - Control Flow : secret-dep branching remove using CMOV.
  - Response : Fixed ratio between response size and scanned blocks.

{% asset_img BITE_sysModel.png}

## Solution1 - Scanning Window
{% asset_img scanningWindow.png %}
{% asset_img scanningWindow2.png %}

## Solution2 - Oblivious Database
{% asset_img obliviousDatabase.png %}
{% asset_img obliviousDatabase2.png %}

이 논문을 읽고...

사용자 개인 정보 보호는 Bitcoin과 같은 분산 통화의 주요 목표 중 하나이다. 그러나 결제 확인에는 전체 체인을 다운로드하여 처리해야하므로 대부분의 모바일 클라이언트에서는 불가능하다.
따라서 모든 인기 있는 블록 체인은 경량 클라이언트가 전체 노드의 도움으로 트랜잭션을 확인할 수 있는 단순화 된 검증 모드를 지원한다.
불행히도, 그러한 지불 확인은 사용자 개인 정보를 보존하지 않으므로 비트 코인과 같은 시스템을 사용하는 주요 이점 중 하나를 무효화 한다. 이 논문에서는 신뢰할 수 있는 execution을 사용하여 경량 클라이언트의 개인 정보를 개선하기 위한 새로운 접근 방식을 제안했다.
이 논문은 그들의 솔루션이 강력한 개인 정보 보호를 제공하고 현재 경량 클라이언트의 성능을 추가로 향상시킨다고 한다. BITE는 모바일 장치와 같은 가벼운 클라이언트의 개인 정보를 보호하는 실용적인 솔루션이라고 생각한다.