# infra_ha_suite — Ansible Collection

DNS, FTP, Mail, Web, Web LB(이중화), NTP, DB, 모니터링까지 **한 번의 플레이북 실행으로** 설치·구성해주는 인프라 자동화 컬렉션.

- **Ansible Galaxy**: https://galaxy.ansible.com/ui/repo/published/is_hwa/infra_ha_suite/
- **Collection**: `is_hwa.infra_ha_suite`
- **Ansible**: 2.14+ 권장 / Python 3.9+
- **지원 OS**: RHEL/CentOS 8 ~ 9, Rocky/Alma 8 ~ 9, Ubuntu 20.04/22.04 (롤별 상이)

## 특징
- **원클릭 배포**: `ansible-playbook -i inventory/hosts.ini site.yml` 한 번으로 끝.
- **Idempotent**: 재실행 시 변경 최소화.
- **역할 분리**: `dns`, `ftp`, `mail`, `web`, `web_lb`, `ntp`, `db`, `monitoring`.
- **옵션 기반 구성**: `group_vars`/`host_vars`로 기능 On/Off 및 파라미터 제어.
- **HA 설계**: Web LB(HAProxy) + 선택적 VIP(keepalived) 지원(옵션).

## 인프라 구성

- **DNS**: ansible6, 내부 도메인 example.com
- **FTP**: ansible1, vsftpd, 테스트 계정 ftpuser
- **Mail**: ansible1, Postfix+Dovecot, justice 계정
- **Web/Tomcat**: ansible2, Apache+Tomcat9, AJP/프록시
- **LB/NFS**: ansible5, HAProxy+NFS `/www1`
- **Web**: ansible1/6, httpd, index.html 배포
- **NTP**: ansible4 서버 + 클라이언트, chrony 동기화
- **DB**: ansible3, MariaDB, root 패스워드 설정
- **monitoring**: ansible.example.com, Prometheus+Grafana+Loki

## 빠른 시작 (Quick Start)

### 1) 설치
```bash
# Ansible Galaxy에서 설치
ansible-galaxy collection install is_hwa.infra_ha_suite

# 또는 requirements.yml 사용
# ---
# collections:
#   - name: is_hwa.infra_ha_suite
#     version: ">=1.0.0"
ansible-galaxy install -r requirements.yml
```
2) inventory 예시
```yaml
[dns]
dns1 ansible_host=192.168.10.11
dns2 ansible_host=192.168.10.12

[ftp]
ftp1 ansible_host=192.168.10.21

[mail]
mail1 ansible_host=192.168.10.31

[web]
web1 ansible_host=192.168.10.41
web2 ansible_host=192.168.10.42

[web_lb]
lb1 ansible_host=192.168.10.51
lb2 ansible_host=192.168.10.52

[ntp]
ntp1 ansible_host=192.168.10.61

[db]
db1 ansible_host=192.168.10.71

[monitoring]
mon1 ansible_host=192.168.10.81
```

3) 플레이북(컬렉션 FQCN 사용)
# playbook.yml
```yaml
---
- name: Build hybrid infra all-in-one
  hosts: all
  become: true
  roles:
    - is_hwa.infra_ha_suite.common
    - { role: is_hwa.infra_ha_suite.dns,         when: "'dns' in group_names" }
    - { role: is_hwa.infra_ha_suite.ftp,         when: "'ftp' in group_names" }
    - { role: is_hwa.infra_ha_suite.mail,        when: "'mail' in group_names" }
    - { role: is_hwa.infra_ha_suite.web,         when: "'web' in group_names" }
    - { role: is_hwa.infra_ha_suite.web_lb,      when: "'web_lb' in group_names" }
    - { role: is_hwa.infra_ha_suite.ntp,         when: "'ntp' in group_names" }
    - { role: is_hwa.infra_ha_suite.db,          when: "'db' in group_names" }
    - { role: is_hwa.infra_ha_suite.monitoring,  when: "'monitoring' in group_names" }
```
4) 릴리스(Ansible Galaxy 퍼블리시)
# galaxy.yml 의 version 갱신(semver)
ansible-galaxy collection build
ansible-galaxy collection publish dist/is_hwa-infra_ha_suite-<VER>.tar.gz --token "$GALAXY_API_TOKEN"

