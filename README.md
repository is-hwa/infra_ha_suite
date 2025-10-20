# infra_ha_suite — Ansible Collection

DNS, FTP, Mail, Web, Web LB(이중화), NTP, DB, 모니터링까지 **한 번의 플레이북 실행으로** 설치·구성해주는 인프라 자동화 컬렉션.
[![Galaxy](https://galaxy.ansible.com/ui/repo/published/is_hwa/infra_ha_suite/)](GALAXY_URL)
- **Collection**: `is_hwa.infra_ha_suite`
- **Ansible**: 2.14+ 권장 / Python 3.9+
- **지원 OS**: RHEL/CentOS 8 ~ 9, Rocky/Alma 8 ~ 9, Ubuntu 20.04/22.04 (롤별 상이)

## 특징
- **원클릭 배포**: `ansible-playbook -i inventory/hosts.ini site.yml` 한 번으로 끝.
- **Idempotent**: 재실행 시 변경 최소화.
- **역할 분리**: `dns`, `ftp`, `mail`, `web`, `web_lb`, `ntp`, `db`, `monitoring`.
- **옵션 기반 구성**: `group_vars`/`host_vars`로 기능 On/Off 및 파라미터 제어.
- **HA 설계**: Web LB(HAProxy) + 선택적 VIP(keepalived) 지원(옵션).

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
