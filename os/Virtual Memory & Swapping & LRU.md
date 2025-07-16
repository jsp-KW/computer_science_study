Virtual Memory & Swapping & LRU Summary

✅ 가상 메모리(Virtual Memory)

실제 RAM보다 큰 메모리처럼 동작하도록 설계된 메모리 관리 기법.

프로세스 전체를 메모리에 올리지 않고 필요한 부분만 올려 실행 가능.

페이징(Paging) 기법으로 페이지 단위로 메모리 관리.

✅ 스와핑(Swapping)

오래 사용하지 않은 프로세스/페이지를 디스크(swap 공간)로 내리고(Swap Out), 필요한 순간 다시 메모리에 올림(Swap In).

스왑 아웃 → RAM 공간 확보 → 더 많은 프로세스 실행 가능.

디스크가 SSD일수록 스왑 성능은 상대적으로 빠름, HDD일 경우 시스템 심각하게 느려질 수 있음.

✅ swappiness

Linux 시스템에서 스왑 동작 빈도 조절하는 커널 파라미터.

값 범위: 0 ~ 100

0: 가능한 한 스왑 사용 안 함

60: 기본값, 적당히 스왑 사용

100: 최대한 적극적으로 스왑 사용

# 현재 swappiness 확인
cat /proc/sys/vm/swappiness

# swappiness 임시 변경
sudo sysctl vm.swappiness=10

✅ 페이지 테이블 계층 구조

다단계 페이지 테이블 (Multi-Level Page Table)

32bit 시스템: 2단계, 64bit 시스템: 보통 4단계 (PML4 → PDPT → PD → PT)

페이지 폴트 발생 시 각 계층을 메모리에서 조회 → 메모리 접근 횟수 증가

최악의 경우 각 계층마다 페이지 폴트 발생 가능 → 디스크 접근 증가

✅ 페이지 교체 알고리즘 - LRU

LRU (Least Recently Used): 가장 오래 사용하지 않은 페이지부터 교체.

LRU 작동 흐름

상황

동작

이미 있는 페이지

제거 후 맨 앞에 추가 (최근 사용 처리)

없는 페이지

페이지 폴트 발생 → 뒤에서 제거 후 맨 앞에 추가

✅ Python 쉬운 구현 예

capacity = 3
pages = [2,3,1,3,5,2,3,4,2,3]
frame = []
page_fault = 0

for p in pages:
    if p in frame:
        frame.remove(p)
    else:
        page_fault += 1
        if len(frame) >= capacity:
            frame.pop()
    frame.insert(0, p)

print("Page fault:", page_fault)

✅ 전체 개념 한줄 정리

가상메모리는 전체 프로세스를 올리지 않아도 실행 가능, 스왑으로 공간 확보, LRU로 최근 사용 여부에 따라 교체

