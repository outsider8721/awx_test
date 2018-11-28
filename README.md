
필요한 package 설치
=

- $ sudo yum -y install epel-release
- $ sudo yum -y install git gettext ansible docker nodejs npm gcc-c++ bzip2
- $ sudo yum -y install python-docker-py

docker 데몬 시작
=
- $ sudo systemctl start docker
- $ sudo systemctl enable docker

awx clone
=

$ git clone https://github.com/ansible/awx.git
$ cd awx/installer/

inventory 편집
=
- 설치하려는 환경이 proxy를 통해서 외부에 엑세스 한다면 : host_port, http_proxy, https_proxy, no_proxy 설정을 자체 환경에 맞게 변경
- postgres_data_dir 위치 변경 : default => /tmp 변경 필요
- use_docker_compose=true : installer playbook 을 실행하여 나의 환경에 맞춰진 docker-compose.yml 파일을 생성하는것을 enable 하는 옵션. 이후에는 docker-compose를 통해서 컨테이너를 제어하자.
- docker_compose_dir=/var/lib/awx : docker-compose를 사용하기로 했다면, docker-compose.yml 파일이 생성될 디렉토리를 지정
- dockerhub_version : docker hub 에서 받아올 awx 버전.

example
==
- dockerhub_version=1.0.4.83
- postgres_data_dir=/var/awx/pgdocker
- use_docker_compose=true
- docker_compose_dir=/var/lib/awx

playbook을 실행해서 AWX를 설치
=

- ansible-playbook -i inventory install.yml
= docker-compose up -d


웹 접속
=
- 초기 접속 계정은 admin/password 이다.

project 설정
=

- credential 추가
- 항목	설정값
- name	Ansible Control Node Credential
- description	playbook 을 실행하기 위해서 control node 에 접속하기 위한 credential
- organization	Default
- type	machine
- username	ssh 계정명
- password	ssh 패스워드
- PRIVILEGE ESCALATION	sudo
- project 추가
- 항목	설정값
- name	Project Name
- description	프로젝트 설명
- organization	Default
- SCM type	git
- SCM Url	ansible playbook github repo 주소
- SCM Branch	
- SCM Update Options	Clean, Update on Launch 체크

inventory 추가
=
- ansible 을 CLI 에서 다룰 때는 hosts 파일을 지정하면 되었지만, awx 에서는 target node들을 db로 관리. 따라서 연결된 SCM 에서 hosts 파일을 읽어 오거나 (매번 playbook 이 실행되기 전에 hosts 내역을 업데이트 한다)/dynamic inventory(클라우드와 같이 target node 들이 유연하게 생성/삭제되어 변경되는 경우)/ 또는 수동으로 직접 관리.

job template 추가
=
- 항목	설정값
- name	실행할 playbook 의 제목
- description	실행할 playbook 의 설명
- job type	run (check 인경우에는 dry run 만 수행)
- inventory	UI 상에서 추가한 inventory 연결
- project	Project Name
- playbook	프로젝트에 연결된 SCM(git)에서 playbook 리스트를 자동으로 불러와 그 중 하나를 선택한다.
- machen Credential	Ansible Control Node Credential : playbook 을 실행하기 위한 control node 에 접속하기 위한 계정정보
- Options	Enable Privilges Escalation (sudo 필요시 체크)
- run job template
- template 에서 등록한 job을 run(로켓 모양 아이콘 클릭) 하면 된다. 이렇게 되면 jobs 메뉴에 실행 이력이 추가된다.
- Identity added: /tmp/awx_40_wPmxLi/credential_6 (/tmp/awx_40_wPmxLi/credential_6)
- Using /etc/ansible/ansible.cfg as config file
