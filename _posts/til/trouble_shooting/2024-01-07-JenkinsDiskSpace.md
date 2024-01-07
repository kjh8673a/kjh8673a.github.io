---
layout: post
title: "[트러블슈팅] 젠킨스 디스크 용량 부족"
date: 2023-11-20 11:30:00 +0900
categories: til
tags: trouble_shooting
---

**Disk space is below...**

### 내용

![image](https://github.com/kjh8673a/kjh8673a.github.io/assets/76678030/5064f26c-4876-47dd-a9f5-0c614ea3ab40)

프로젝트를 빌드하려고했는데 젠킨스의 built-in-node가 용량 부족 문제로 실행이 되지 않는 상황이 발생했다.

### 해결

ec2 볼륨을 수정해주는 것으로 해결했다.

기존에는 8GB 볼륨으로 되어있었는데 20GB로 늘려주었다.

과정은 다음과 같다.

1. ec2 콘솔에 접속 -> 인스턴스 선택.

2. 세부정보의 스토리지탭으로 이동.

3. 블록 디바이스에서 볼륨ID선택하여 진입.

4. 수정눌러 수정 페이지 진입 후 크기 설정.

ec2에서 볼륨은 늘렸지만 ssh 터미널에서 다시 설정해주어야한다.

```bash
# 하드디스크 용량 확인
$ df -hT

# 파티션 볼륨 확인
$ lsblk

# 파티션 크기 조정
$ sudo growpart /dev/nvme0n1 1

# ex4 파일 시스템 확장
$ sudo resize2fs /dev/nvme0n1p1

```

명령어를 통해 파티션 크기와 파일시스템을 확장하고

볼륨이 설정한대로 적용되었는지 확인하면 된다.

그 후, ec2 콘솔로 돌아가 인스턴스 재부팅을 해주면 끝.

젠킨스에 다시 접속하면 문제가 해결되어있다.
