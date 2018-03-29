---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-23"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# SSH 액세스에서 루트 사용자 제한

{{site.data.keyword.cloud}} 네트워크의 모든 Linux 시스템에는 관리 권한을 가진 루트 사용자가 있습니다. Linux 내에서 wheel 그룹을 작성할 수 있으며, 이 그룹은 루트 사용자 신임 정보를 사용할 필요 없이 "sudo"를 통해 루트 사용자의 권한과 유사한 권한을 사용자에게 제공합니다. 루트의 sudo 권한을 가진 wheel 그룹을 작성한 후 SSH 액세스에서 해당 그룹의 사용자를 제한할 수 있습니다. 이와 같이 사용자를 제한하여 네트워크 접근성과 관련된 보안 취약점으로부터 디바이스를 보호합니다. wheel 그룹의 일부인 사용자는 여전히 언제든 디바이스에서 관리 기능을 수행할 수 있습니다. 
{:shortdesc}

다음 단계에 따라 SSH 액세스에서 루트 사용자를 제한하십시오.

1. 'etc/group' 파일을 열고 wheel 그룹을 정의하는 행이 포함되어 있는지 확인하십시오.
```
wheel:x:10:root
```
  
    이 행이 파일에 없는 경우 이를 작성하십시오.

2. wheel 그룹 행에 하나 이상의 사용자를 추가하십시오.
```
wheel:x:10:root, user1
```
    
    wheel 그룹에 추가된 사용자는 루트 사용자와 동일한 권한을 갖게 되지만 시스템에 액세스할 때 고유 사용자 이름을 사용합니다.
3. `:wq` 명령을 실행하여 변경사항을 저장하고 파일을 종료하십시오.
4. `/etc/ssh/sshd_config`를 여십시오.
5. `PermitRootLogin yes` 행을 변경하여 `PermitRootLogin no`를 읽으십시오.
6. `:wq` 명령을 실행하여 변경사항을 저장하고 파일을 종료하십시오.
7. **명령행**에 `visudo`를 입력하여 명령 권한을 생성하십시오.
8. 다음 행에서 **해시(#)**를 제거하여 행을 주석 해제하십시오.
```
# %wheel ALL=(ALL) ALL
```
  
    **참고:** 이 행을 주석 해제하면 wheel 그룹의 사용자가 모든 명령을 실행할 수 있습니다.
    
9. `:wq` 명령을 실행하여 변경사항을 저장하고 파일을 종료하십시오.
10. 명령행에서 다음 명령을 실행하십시오.
```
vi /etc/pam.d/su
```
  
11. 다음 행에서 **해시(#)**를 제거하여 행을 주석 해제하십시오.
```
#auth required pam_wheel.so use_uid
```

    **참고:** 이 행을 주석 해제하려면 사용자가 모든 명령을 실행할 권한이 있는 wheel 그룹의 일부여야 합니다.
12. `:wq` 명령을 실행하여 변경사항을 저장하고 파일을 종료하십시오.
13. 다음 명령을 실행하여 모든 변경사항을 저장하고 SSH를 다시 시작하십시오.
```
# etc/init.d/ssh restart
```

## 다음 단계

SSH 액세스에서 루트 사용자를 제한하면 루트 사용자가 SSH에 로그인할 수 없습니다. 사용자가 현재 루트 사용자로 SSH를 통해 서버에 액세스할 수 있는 경우, 이전 프로시저에서 SSH를 다시 시작하면 연결할 수 없습니다. 앞으로 루트 사용자를 통해 SSH에 연결할 수 없게 됩니다. 이러한 변경을 취소하려면 단계를 반복하여 `PermitRootLogin` no를 다시 `PermitRootLogin` yes로 변경하십시오.