💡 Virtual Memory, Swapping, Page Replacement Summary

✅ 가상 메모리 (Virtual Memory)

RAM보다 큰 메모리를 제공하는 논리적 메모리 관리 기법

프로세스 전체를 메모리에 올리지 않고, 필요한 페이지만 메모리에 적재하여 실행

페이징(Paging) 방식을 사용하여 가상 주소를 페이지 단위로 관리

장점: 메모리 공간 절약, 프로세스 격리 보호, 더 많은 프로세스 실행 가능

✅ 스와핑 (Swapping)

오래 사용하지 않는 프로세스나 페이지를 디스크의 swap 공간으로 내리고(Swap Out), 필요할 때 다시 메모리로 불러오는(Swap In) 기법

스왑 아웃 시 RAM 공간이 확보되어 더 많은 프로세스가 실행 가능

SSD → 스왑 동작이 빠름 / HDD → 느려져서 시스템 전체가 버벅일 수 있음

💡 Swap 공간은 실제 디스크 파티션 또는 swap 파일로 제공됨

✅ Swappiness (Linux 기준)

Swappiness는 OS가 얼마나 적극적으로 swap을 사용할지 결정하는 파라미터

범위: 0 ~ 100

0: 거의 swap 사용 안 함 (RAM 다 찬 후에만 스왑)

60: (기본값) RAM이 일정 부분 차면 스왑 사용

100: RAM이 남아도 swap 적극 사용

# swappiness 확인
cat /proc/sys/vm/swappiness

# swappiness 임시 변경
sudo sysctl vm.swappiness=10

📌 권장: 데스크탑 환경에서는 10~20 추천, 서버 환경에서는 상황에 따라 다름

✅ 다단계 페이지 테이블 구조

가상 주소 → 물리 주소 변환 과정에서 계층적으로 페이지 테이블 사용

32bit 시스템: 주로 2단계, 64bit 시스템: 4단계 (PML4 → PDPT → PD → PT)

페이지 폴트 발생 시 각 계층을 메모리에서 탐색해야 함 → 메모리 접근 횟수 증가

최악의 경우 각 계층마다 page fault 발생 → 추가적인 디스크 접근 발생

페이지 테이블 예시 (64bit 기준)

가상 주소 → [PML4 index | PDPT index | PD index | PT index | offset]

💡 계층이 많을수록 page fault 발생 시 오버헤드 증가하지만, 큰 가상 메모리 공간 지원이 가능

✅ 페이지 교체 알고리즘: LRU (Least Recently Used)

가장 오래 사용되지 않은 페이지를 먼저 교체하는 알고리즘

최근 사용 여부를 기준으로 교체 대상 결정

LRU 동작 흐름

상황

동작

이미 있는 페이지 참조

리스트에서 제거 후 맨 앞에 추가 (최근 사용 처리)

없는 페이지 참조 (page fault)

페이지 폴트 발생 → 프레임 꽉 차면 맨 뒤 페이지 제거 후 맨 앞에 추가

Python 쉬운 구현 예시 (List 활용)

capacity = 3
pages = [2,3,1,3,5,2,3,4,2,3]
frame = []
page_fault = 0

for p in pages:
    if p in frame:
        frame.remove(p)  # 최근 사용 처리
    else:
        page_fault += 1
        if len(frame) >= capacity:
            frame.pop()  # 가장 오래된 페이지 제거
    frame.insert(0, p)  # 맨 앞에 추가

print("Page fault:", page_fault)

추가 참고

O(N) 리스트 기반 구현 → 간단 실습용

O(1) 효율적 구현 → OrderedDict, LinkedHashMap 사용 (대규모 최적화)

✅ 전체 핵심 정리

가상 메모리는 RAM보다 큰 메모리를 제공하며, 필요한 페이지만 메모리에 올려 실행

스와핑은 비활성 페이지를 디스크로 내려 공간 확보

Swappiness로 스왑 빈도 조절 가능

페이지 테이블 계층으로 메모리 매핑 관리 (계층 많을수록 page fault 비용 증가)

페이지 교체는 LRU로 최근 사용 순서를 반영해 교체

