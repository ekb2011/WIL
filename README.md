# **IaC(Infrastructure as Code)**

## **IaC 정의**

인프라 구성을 프로그래밍으로 처리하여 인프라의 유연성과 확장성 부여

## **IaC의 장점**

* **신속성**

    업데이트 배포의 시스템화 → 절차 간소화

* **효율성**
    모듈화를 통한 개발 시간 단축

* **위험성 감소**
    Human Error로 인한 오류 방지
<br/>
<br/>

## **IaC Tool 비교**


|  | **Chef** | **Puppet** | **Ansible** | **Saltstack** | **Terraform** |
| --- | --- | --- | --- | --- | --- |
| Cloud |  | All | All | All | All |
| Type | Config Mgmt | Config Mgmt | Config Mgmt | Config Mgmt | Config Mgmt |
| Infrastructure | Mutable | Mutable | Mutable | Mutable | Immutable |
| Language | Procedual | Declarative | Procedual | Declarative | Declarative |
| Architecture | Client/Server | Client/Server | Client | Client | Client |
| Lifecycle Management | No | No | No | No | Yes |
| VM Provisioning | Partial | Partial | Partial | Partial | Yes |
| Networking | Partial | Partial | Partial | Partial | Yes |
| Storage Management | Partial | Partial | Partial | Partial | Yes |
| Packaging | Yes | Yes | Yes | Yes | Partial |
| Templating | Yes | Yes | Yes | Yes | Partial |
| Service Provisioning | Yes | Yes | Yes | Yes | Yes |

* Procedual Style Language - scripting /  Declarative Style Language - background
* Mutable - 설치 후 변경이 가능 / Immutable - 설치 후 변경이 불가능. 업데이트 시 새로 생성

# **Chef**

## **System Requirements && Supporting Platforms**

* System Requirements
    * Minimum - RAM(2GB) / Disk(4GB)
    * Recommended - RAM(4GB) / Disk(8GB)

* Host OS & Target OS
    * Microsoft Windows - 10, Server 2008 R2, Server 2012, Server 2012 R2, Server 2016
    * CentOS - 6.x, 7.x
    * Ubuntu - 14.04, 16.04, 18.04
    * Debian - 7.x, 8.x, 9.x


## **Chef 구조**

![](https://docs.google.com/drawings/pub?id=1_fvo_dFcqZIJntmAD5NH-QPMYnl84nUTKYKdIHO5CdM&w=751&h=393)

* **Chef Server**
    레시피, 설정 데이터를 저장하는 중앙 저장소
    코드 저장소는 REST API를 활용하여 접근
    Node 정보 저장을 위해 couchDB 사용
    <br/>
* **Chef Client(Node)**
    관리하려는 노드(서버, 인스턴스)
    최신 코드를 확인하고 실행
    <br/>
* **Workstation**
    개발이 이루어지는 IDE
    Knife & Web Console 상에서 수행
<br/>
* Chef Client가 Chef Server에 등록된 데이터를 PULL / 구성 변경은 Workstation에서 knife 명령어로 Chef Server에 통지 및 등록
<br/>
* Workstation에 등록된 Client들에 스크립트를 날려 서버 상의 명령 수행(여러 노드에서 한번에 처리 가능)

## **Chef 구성 요소**
![](http://www.mimul.com/pebble/default/images/blog/cloud/chef_relationship.png)
<br/>
* **Nodes**

    Chef Client가 설치되는 호스트
* **Certificates**
    SSL 인증서 저장 디렉토리
* **Run List**
    로직을 담고 있으며, Node 자동화 수행
* **Roles**
    같은 특성(DB, Web 등)을 갖는 Node들끼리 그룹으로 관리

* **Cookbooks**
    애플리케이션 단위로 개발, 배포의 기본 단위
    recipe, resource definition, attribute, library, cookbook file, template 등으로 구성


* **Resources**
    작업의 기본 단위, 자동화를 위해 많이 사용하는 코드를 라이브러리 형태로 개발해둔 것
    멀티 플랫폼 지향

* **Data Bags**
    Chef Server에 저장되는 JSON 형식(Key-Value)의 데이터
    모든 Cookbook에서 전역변수처럼 사용

## **Cookbook 구성 요소**
![](http://www.mimul.com/pebble/default/images/blog/cloud/cookbook.png)
* **Recipes**
    설정 로직을 담고 있는 코드
    Ruby 언어로 attribute, resource를 활용하여 구성
* **Attributes**
    Recipe에서 사용할 변수
* **Definitions**
    기존 Resource를 결합하여 재사용 가능한 새로운 Resource 정의
* **Files**
    Recipe에서 사용할 파일(설정 파일, 패키지 파일 등)이 저장되는 곳
* **Libraries**
    리소스 기능들을 클래스화한 것. Recipe에서 호출하여 사용
* **Templates**
    Attribute에 따라 내용을 편집하는 전체의 설정 파일 템플릿, erb 형식으로 작성
* **Providers**
    Resource를 받고 Node의 현재 상태와 비교하여 Resource의 동작을 수행. Resource의 이행 도구
* **Metadata**
    Chef에 Recipe 및 종속성 버전 제약, 지원 플랫폼, Cookbook 간의 의존 관계를 전달

## **작성 순서**
Cookbook Template Import from Github → Attribute 정의 → Template 파일 생성 → Recipe 작성 → Metadata 작성 → Cookbook 업로드

## **Chef 설치 및 설정**

* Server, Workstation, Node를 서로 다른 Instance에 설치
<br/>
* Chef Server, Workstation, Node의 hostname과 IP 주소를 /etc/hosts에 추가

### **Chef Server**
    
* 설치
```
$wget https://packages.chef.io/files/stable/chef-server/12.18.14/el/6/chef-server-core-12.18.14-1.el6.x86_64.rpm
$rpm -ivh chef-server-core-12.18.14-1.el6.x86_64.rpm
$chef-server-ctl reconfigure
```
* Web UI(Chef Manage) 설치 및 로그인(Optional)
    * 계정, 그룹의 생성 및 설정, 클라이언트 관리를 Web UI로 제공. 유료 서비스
```
$chef-server-ctl install chef-manage
$chef-manage-ctl reconfigure
$chef-server-ctl reconfigure
```
```
Web Browser 상에서 https://IP_Address/login으로 실행
```

* Admin 계정 생성 및 그룹 설정
```
$chef-server-ctl user-create USER_NAME FIRST_NAME LAST_NAME EMAIL 'PASSWORD' -f PATH_FILE_NAME
$chef-server-ctl org-create nhnent "NHNEnt" --association_user admin -f /etc/chef-server/nhnent-validator.pem

```
* 생성된 User key, Organization pem 파일을 Chef Workstation과 Chef Client로 전송

### **Chef Workstation**

* 설치
```
$wget https://packages.chef.io/stable/el/7/chefdk-0.19.6-1.el7.x86_64.rpm
$rpm -ivh chefdk-0.19.6-1.el7.x86_64.rpm
$chef verify
```
* knife.rb 파일 생성
    * Knife - Chef Server와 Workstation 간의 CMD Interface
```
$knife configure


```
* Knife 접속
```
$knife ssl fetch
$knife ssl check
```
* Cookbook 생성

```
$chef generate cookbook hello
$vim /root/chef-repo/.chef/hello/recipes/default.rb
```
```
execute 'Execute Sample Script' do
        user 'root'
        cwd '/root'
        command 'uname -a'
end
```
* Cookbook 업로드
```
$knife cookbook upload -a
```


### **Client(Node)**

* Chef-Client 설치
```
$wget https://packages.chef.io/files/stable/chef/14.7.17/el/6/chef-14.7.17-1.el6.x86_64.rpm
$rpm -ivh chef-14.7.17-1.el6.x86_64.rpm
```
* Chef-Server의 ssl 가져오기
```
$knife ssl fetch -s https://chef-server
```
* Certification 확인
```
$knife ssl check -s https://chef-server
```
* 설정 파일
```
$vim /etc/chef/client.rb
```
```
log_level        :info
log_location     STDOUT
node_name        'gibonglim'
trusted_certs_dir '/root/.chef/trusted_certs'
chef_server_url  'https://133.186.241.155:443/organizations/nhnent'
validation_client_name 'nhnent-validator'
validation_key '/etc/chef/nhnent-validator.pem'
client_key '/etc/chef/gibonglim.pem'

```
* Node를 Chef-Server에 추가
```
$chef-client -S https://133.186.241.155:443/organizations/nhnent -K /etc/chef/nhnent-validator.pem
```

* Chef-Workstation에서 추가된 Node 정보 확인
```
$knife node show gibonglim
```
```
Node Name:   gibonglim
Environment: _default
FQDN:        chef-node
IP:          192.168.0.78
Run List:
Roles:
Recipes:
Platform:    centos 6.10
Tags:

```

## **Cookbook 생성 및 실행**
* Cookbook 생성 및 추가
```
$chef generate cookbook COOKBOOK_NAME
$vim /etc/chef-server/COOKBOOK_NAME/recipes/default.rb

```
* Chef-Node에서 리눅스 버전 확인하는 명령어를 Cookbook으로 생성
```
execute 'Execute Sample Script' do
        user 'root'
        cwd '/root'
        command 'uname -a'
end

```
* Cookbook 업로드
```
$knife cookbook upload hello -o /etc/chef-server

```
```
Uploading hello        [0.1.0]
Uploaded 1 cookbook.
```
```
$knife node run_list add gibonglim 'recipe[hello]'
```
```
gibonglim:
  run_list: recipe[hello]
```
```
$knife node show gibonglim
```
```
Node Name:   gibonglim
Environment: _default
FQDN:        chef-node
IP:          192.168.0.78
Run List:    recipe[hello]
Roles:
Recipes:     hello, hello::default
Platform:    centos 6.10
Tags:

```
* 업로드된 Cookbook을 Node에서 실행

```
$chef-client
```
```
Starting Chef Client, version 14.7.17
[2018-11-27T02:04:57+09:00] INFO: *** Chef 14.7.17 ***
[2018-11-27T02:04:57+09:00] INFO: Platform: x86_64-linux
[2018-11-27T02:04:57+09:00] INFO: Chef-client pid: 8764
[2018-11-27T02:04:57+09:00] INFO: The plugin path /etc/chef/ohai/plugins does not exist. Skipping...
[2018-11-27T02:04:58+09:00] INFO: Run List is [recipe[hello]]
[2018-11-27T02:04:58+09:00] INFO: Run List expands to [hello]
[2018-11-27T02:04:58+09:00] INFO: Starting Chef Run for gibonglim
[2018-11-27T02:04:58+09:00] INFO: Running start handlers
[2018-11-27T02:04:58+09:00] INFO: Start handlers complete.
[2018-11-27T02:04:58+09:00] INFO: Error while reporting run start to Data Collector. URL: https://133.186.241.155:443/organizations/nhnent/data-collector Exception: 404 -- 404 "Not Found"  (This is normal if you do not have Chef Automate)
resolving cookbooks for run list: ["hello"]
[2018-11-27T02:04:58+09:00] INFO: Loading cookbooks [hello@0.1.0]
Synchronizing Cookbooks:
[2018-11-27T02:04:58+09:00] INFO: Storing updated cookbooks/hello/recipes/default.rb in the cache.
[2018-11-27T02:04:58+09:00] INFO: Storing updated cookbooks/hello/chefignore in the cache.
[2018-11-27T02:04:58+09:00] INFO: Storing updated cookbooks/hello/test/integration/default/default_test.rb in the cache.
[2018-11-27T02:04:58+09:00] INFO: Storing updated cookbooks/hello/metadata.rb in the cache.
[2018-11-27T02:04:58+09:00] INFO: Storing updated cookbooks/hello/Berksfile in the cache.
[2018-11-27T02:04:58+09:00] INFO: Storing updated cookbooks/hello/CHANGELOG.md in the cache.
[2018-11-27T02:04:58+09:00] INFO: Storing updated cookbooks/hello/.kitchen.yml in the cache.
[2018-11-27T02:04:58+09:00] INFO: Storing updated cookbooks/hello/spec/unit/recipes/default_spec.rb in the cache.
[2018-11-27T02:04:58+09:00] INFO: Storing updated cookbooks/hello/LICENSE in the cache.
[2018-11-27T02:04:58+09:00] INFO: Storing updated cookbooks/hello/README.md in the cache.
[2018-11-27T02:04:58+09:00] INFO: Storing updated cookbooks/hello/spec/spec_helper.rb in the cache.
  - hello (0.1.0)
Installing Cookbook Gems:
Compiling Cookbooks...
Converging 1 resources
Recipe: hello::default
  * execute[Execute Sample Script] action run[2018-11-27T02:04:58+09:00] INFO: Processing execute[Execute Sample Script] action run (hello::default line 7)

    [execute] Linux chef-node 2.6.32-754.6.3.el6.x86_64 #1 SMP Tue Oct 9 17:27:49 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
[2018-11-27T02:04:58+09:00] INFO: execute[Execute Sample Script] ran successfully
    - execute uname -a
[2018-11-27T02:04:58+09:00] INFO: Chef Run complete in 0.339810451 seconds

Running handlers:
[2018-11-27T02:04:58+09:00] INFO: Running report handlers
Running handlers complete
[2018-11-27T02:04:58+09:00] INFO: Report handlers complete
Chef Client finished, 1/1 resources updated in 01 seconds

```
* Cookbook을 Workstation에서 원격으로 실행
```
$knife bootstrap -N chef-node
```
```
ERROR: Must pass an FQDN or ip to bootstrap

```

* 단순 명령어 실행

```
$knife ssh "name:gibonglim" "uname -a" -x root
```
```
WARNING: Failed to connect to chef-node -- Net::SSH::AuthenticationFailed: Authentication failed for user root@chef-node@chef-node
```
# **도입 사례**
## **AWS**
![](https://d1.awsstatic.com/diagrams/product-page-diagrams/Diagram_ops-works_chef-server.e745bce7c88cec36ddeb6bd60eee43d36880ea58.png)
* 인스턴스, on-premise 서버의 설정, 배포 등
### **1 Chef Server to serve all parts**
![](https://d2908q01vomqb2.cloudfront.net/972a67c48192728a34979d9a35164c1295401b71/2017/12/27/single_chef.png)
* 서로 다른 Region의 각 인스턴스들을 통합 관리, 비즈니스 별 특화된 관리를 통해 효율 추구

### **1 Chef Server per parts**
![](https://d2908q01vomqb2.cloudfront.net/972a67c48192728a34979d9a35164c1295401b71/2017/12/27/Untitled_Diagram.png)
* 비즈니스 별 독립된 리소스 사용

### **Centralized Cookbook Repository**
![](https://d2908q01vomqb2.cloudfront.net/972a67c48192728a34979d9a35164c1295401b71/2017/12/27/git_mutlichef_cropped.png)

## **Azure**
![](https://docs.microsoft.com/ko-kr/azure/virtual-machines/windows/media/chef-automation/2.png)


# **도입 방안**

## Multi Region을 관리할 수 있는 환경 제공
## 기능
### 인스턴스 관리
* 여러 개의 인스턴스를 관리, 운영하는 Workload 감소
* 서비스 별 설정 모듈화
```
#nginx 설치 Recipe 예시

package "nginx" do
  action :install
end

service "nginx" do
  supports :status => true, :restart => true, :reload => true
  action [ :enable, :start ]
end

# Chef는 운영체제에 따라 centos는 yum repository에서, debian은 apt에서 패키지를 찾아서 설치하고, init 스크립트를 자동으로 수정하는 기능 제공
```
* 관리하는 각 Node 별 상태를 Recipe에서 관리
    * 인스턴스 이미지 생성 시 이미지가 어떤 상태를 가지고 있는지 파악이 어려움
    * 서버 백업 및 증축 등의 작업 시 인스턴스의 이미지를 생성한 시점부터의 변화를 인지하고 있고, 이를 수동으로 설정해야하는 문제 방지
* 배포해야 하는 패키지, tar 파일 등을 Chef Server에서 보관 가능
### 인스턴스 생성
* Cookbook을 Workstation에서 호출, 인스턴스 생성

#### AWS 활용 예시


```
$knife ec2 server create
  --image ami-ce7b6fba
  --flavor m1.small
  --region eu-west-1
  --server-connect-attribute private_ip_address
  --ssh-gateway user@gateway.ec2.example.com
  --ssh-user ubuntu
  --identity-file ~/.ssh/example.pem
  --subnet subnet-8d034be5
  --environment production
  --node-name web1
  --run-list 'role[base],role[web_server]'
```
#### Azure 활용 예시
```
#Cookbook 생성

powershell_script 'Install IIS' do
     action :run
     code 'add-windowsfeature Web-Server'
end

service 'w3svc' do
     action [ :enable, :start ]
end

template 'c:\inetpub\wwwroot\Default.htm' do
     source 'Default.htm.erb'
     rights :read, 'Everyone'
end
```

```
$knife azure server create
  --azure-dns-name 'diegotest01'
  --azure-vm-name 'testserver01'
  --azure-vm-size 'Small'
  --azure-storage-account 'portalvhdsxxxx'
  --bootstrap-protocol 'cloud-api'
  --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd'
  --azure-service-location 'Southeast Asia'
  --winrm-user azureuser
  --winrm-password 'myPassword123'
  --tcp-endpoints 80,3389
  --r 'recipe[webserver]'
```
## 적용 방안
### Chef Server
* Chef Manage Tool을 도입하여 Web UI 상에서 User, Organization, pem 키 생성 및 관리 기능 제공
### Chef Workstation
* 별도의 인스턴스 상품화, 고객이 직접 설치해야하는 부담 최소화
* Cookbook을 구성하고, bootstrap을 통해 Node를 효율적으로 관리하는 인스턴스로 활용
### Chef Node
* Node 초기 설정값을 모듈화하여 한번에 설치할 수 있도록 제공
* 설정값을 Customize하는 Guideline 제시

## 장애 대응
### 시나리오
특정 Region의 서버에서 장애 발생 → Shutdown 후 새로운 인스턴스 생성 및 최신 Node상태 복원 → 서비스 재가동

## 기타 세부 고려 사항
* Node의 상태에 대한 버전 관리 여부