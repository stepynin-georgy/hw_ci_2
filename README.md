# Домашнее задание к занятию 9 «Процессы CI/CD»

## Подготовка к выполнению

1. Создайте два VM в Yandex Cloud с параметрами: 2CPU 4RAM Centos7 (остальное по минимальным требованиям).
2. Пропишите в [inventory](./infrastructure/inventory/cicd/hosts.yml) [playbook](./infrastructure/site.yml) созданные хосты.

```
---
all:
  hosts:
    sonar-01:
      ansible_host: 89.169.164.127
    nexus-01:
      ansible_host: 130.193.52.92
  children:
    sonarqube:
      hosts:
        sonar-01:
    nexus:
      hosts:
        nexus-01:
    postgres:
      hosts:
        sonar-01:
  vars:
    ansible_connection_type: paramiko
    ansible_user: user
    ansible_ssh_private_key_file: /opt/ssh_keys/id_rsa
```

3. Добавьте в [files](./infrastructure/files/) файл со своим публичным ключом (id_rsa.pub). Если ключ называется иначе — найдите таску в плейбуке, которая использует id_rsa.pub имя, и исправьте на своё.
4. Запустите playbook, ожидайте успешного завершения.

Немного поработав с репозиториями получилось выполнить это дз на Centos 7.

<details>
<summary>Запуск playbook</summary>
   
```
(venv_ci) root@ansible-ubuntu:/opt/hw_ci_3# ansible-playbook -i infrastructure/inventory/cicd/hosts.yml infrastructure/site.yml
[DEPRECATION WARNING]: Ansible will require Python 3.8 or newer on the controller starting with Ansible 2.12. Current version: 3.6.15 (default, Sep 13 2024, 08:34:12) [GCC 13.2.0].
This feature will be removed from ansible-core in version 2.12. Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
/opt/venv_ci/lib/python3.6/site-packages/ansible/parsing/vault/__init__.py:44: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography. The next release of cryptography will remove support for Python 3.6.
  from cryptography.exceptions import InvalidSignature

PLAY [Get OpenJDK installed] ************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************
ok: [sonar-01]

TASK [install unzip] ********************************************************************************************************************************************************************
ok: [sonar-01]

TASK [Upload .tar.gz file conaining binaries from remote storage] ***********************************************************************************************************************
ok: [sonar-01]

TASK [Ensure installation dir exists] ***************************************************************************************************************************************************
ok: [sonar-01]

TASK [Extract java in the installation directory] ***************************************************************************************************************************************
skipping: [sonar-01]

TASK [Export environment variables] *****************************************************************************************************************************************************
ok: [sonar-01]

PLAY [Get PostgreSQL installed] *********************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************
ok: [sonar-01]

TASK [Install PostgreSQL repos] *********************************************************************************************************************************************************
ok: [sonar-01]

TASK [Install PostgreSQL] ***************************************************************************************************************************************************************
ok: [sonar-01]

TASK [Init template1 DB] ****************************************************************************************************************************************************************
changed: [sonar-01]

TASK [Start pgsql service] **************************************************************************************************************************************************************
ok: [sonar-01]

TASK [Create user in system] ************************************************************************************************************************************************************
ok: [sonar-01]

TASK [Create user for Sonar in PostgreSQL] **********************************************************************************************************************************************
changed: [sonar-01]

TASK [Change password for Sonar user in PostgreSQL] *************************************************************************************************************************************
changed: [sonar-01]

TASK [Create Sonar DB] ******************************************************************************************************************************************************************
changed: [sonar-01]

TASK [Copy pg_hba.conf] *****************************************************************************************************************************************************************
ok: [sonar-01]

PLAY [Prepare Sonar host] ***************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************
ok: [sonar-01]

TASK [Create group in system] ***********************************************************************************************************************************************************
ok: [sonar-01]

TASK [Create user in system] ************************************************************************************************************************************************************
ok: [sonar-01]

TASK [Set up ssh key to access for managed node] ****************************************************************************************************************************************
changed: [sonar-01]

TASK [Allow group to have passwordless sudo] ********************************************************************************************************************************************
ok: [sonar-01]

TASK [Increase Virtual Memory] **********************************************************************************************************************************************************
ok: [sonar-01]

TASK [Reboot VM] ************************************************************************************************************************************************************************
changed: [sonar-01]

PLAY [Get Sonarqube installed] **********************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************
ok: [sonar-01]

TASK [Get distrib ZIP] ******************************************************************************************************************************************************************
changed: [sonar-01]

TASK [Unzip Sonar] **********************************************************************************************************************************************************************
changed: [sonar-01]

TASK [Move Sonar into place.] ***********************************************************************************************************************************************************
changed: [sonar-01]

TASK [Configure SonarQube JDBC settings for PostgreSQL.] ********************************************************************************************************************************
changed: [sonar-01] => (item={'regexp': '^sonar.jdbc.username', 'line': 'sonar.jdbc.username=sonar'})
changed: [sonar-01] => (item={'regexp': '^sonar.jdbc.password', 'line': 'sonar.jdbc.password=sonar'})
changed: [sonar-01] => (item={'regexp': '^sonar.jdbc.url', 'line': 'sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance'})
changed: [sonar-01] => (item={'regexp': '^sonar.web.context', 'line': 'sonar.web.context='})

TASK [Generate wrapper.conf] ************************************************************************************************************************************************************
changed: [sonar-01]

TASK [Symlink sonar bin.] ***************************************************************************************************************************************************************
changed: [sonar-01]

TASK [Copy SonarQube systemd unit file into place (for systemd systems).] ***************************************************************************************************************
changed: [sonar-01]

TASK [Ensure Sonar is running and set to start on boot.] ********************************************************************************************************************************
changed: [sonar-01]

TASK [Allow Sonar time to build on first start.] ****************************************************************************************************************************************
Pausing for 180 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [sonar-01]

TASK [Make sure Sonar is responding on the configured port.] ****************************************************************************************************************************
ok: [sonar-01]

PLAY [Get Nexus installed] **************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************
ok: [nexus-01]

TASK [Create Nexus group] ***************************************************************************************************************************************************************
changed: [nexus-01]

TASK [Create Nexus user] ****************************************************************************************************************************************************************
changed: [nexus-01]

TASK [Install JDK] **********************************************************************************************************************************************************************
changed: [nexus-01]

TASK [Create Nexus directories] *********************************************************************************************************************************************************
changed: [nexus-01] => (item=/home/nexus/log)
changed: [nexus-01] => (item=/home/nexus/sonatype-work/nexus3)
changed: [nexus-01] => (item=/home/nexus/sonatype-work/nexus3/etc)
changed: [nexus-01] => (item=/home/nexus/pkg)
changed: [nexus-01] => (item=/home/nexus/tmp)

TASK [Download Nexus] *******************************************************************************************************************************************************************
[WARNING]: Module remote_tmp /home/nexus/.ansible/tmp did not exist and was created with a mode of 0700, this may cause issues when running as another user. To avoid this, create the
remote_tmp dir with the correct permissions manually
changed: [nexus-01]

TASK [Unpack Nexus] *********************************************************************************************************************************************************************
changed: [nexus-01]

TASK [Link to Nexus Directory] **********************************************************************************************************************************************************
changed: [nexus-01]

TASK [Add NEXUS_HOME for Nexus user] ****************************************************************************************************************************************************
changed: [nexus-01]

TASK [Add run_as_user to Nexus.rc] ******************************************************************************************************************************************************
changed: [nexus-01]

TASK [Raise nofile limit for Nexus user] ************************************************************************************************************************************************
changed: [nexus-01]

TASK [Create Nexus service for SystemD] *************************************************************************************************************************************************
changed: [nexus-01]

TASK [Ensure Nexus service is enabled for SystemD] **************************************************************************************************************************************
changed: [nexus-01]

TASK [Create Nexus vmoptions] ***********************************************************************************************************************************************************
changed: [nexus-01]

TASK [Create Nexus properties] **********************************************************************************************************************************************************
changed: [nexus-01]

TASK [Lower Nexus disk space threshold] *************************************************************************************************************************************************
skipping: [nexus-01]

TASK [Start Nexus service if enabled] ***************************************************************************************************************************************************
changed: [nexus-01]

TASK [Ensure Nexus service is restarted] ************************************************************************************************************************************************
skipping: [nexus-01]

TASK [Wait for Nexus port if started] ***************************************************************************************************************************************************
ok: [nexus-01]

PLAY RECAP ******************************************************************************************************************************************************************************
nexus-01                   : ok=17   changed=15   unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
sonar-01                   : ok=33   changed=14   unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

/opt/venv_ci/bin/ansible-playbook:135: ResourceWarning: unclosed file <_io.BufferedRandom name=5>
  exit_code = cli.run()
```
</details>

5. Проверьте готовность SonarQube через [браузер](http://localhost:9000).

![изображение](https://github.com/stepynin-georgy/hw_ci_2/blob/main/img/Screenshot_21.png)

6. Зайдите под admin\admin, поменяйте пароль на свой.
7. Проверьте готовность Nexus через [бразуер](http://localhost:8081).

![изображение](https://github.com/stepynin-georgy/hw_ci_2/blob/main/img/Screenshot_22.png)

8. Подключитесь под admin\admin123, поменяйте пароль, сохраните анонимный доступ.

## Знакомоство с SonarQube

### Основная часть

1. Создайте новый проект, название произвольное.

![изображение](https://github.com/stepynin-georgy/hw_ci_2/blob/main/img/Screenshot_23.png)

2. Скачайте пакет sonar-scanner, который вам предлагает скачать SonarQube.
4. Сделайте так, чтобы binary был доступен через вызов в shell (или поменяйте переменную PATH, или любой другой, удобный вам способ).
5. Проверьте `sonar-scanner --version`.

![изображение](https://github.com/stepynin-georgy/hw_ci_2/blob/main/img/Screenshot_24.png)

6. Запустите анализатор против кода из директории [example](./example) с дополнительным ключом `-Dsonar.coverage.exclusions=fail.py`.

<details>
<summary>Запуск sonar-scanner</summary>

```
(venv_ci) root@ansible-ubuntu:/opt/hw_ci_3# sonar-scanner -Dsonar.projectKey=hw_ci -Dsonar.login=################################ -Dsonar.host.url=http://89.169.164.127:9000 -Dsonar.coverage.exclusions=fail.py
13:23:22.978 INFO  Scanner configuration file: /opt/hw_ci_3/sonar-scanner-6.1.0.4477-linux-x64/conf/sonar-scanner.properties
13:23:22.981 INFO  Project root configuration file: NONE
13:23:22.999 INFO  SonarScanner CLI 6.1.0.4477
13:23:23.002 INFO  Java 17.0.11 Eclipse Adoptium (64-bit)
13:23:23.003 INFO  Linux 6.8.0-44-generic amd64
13:23:23.028 INFO  User cache: /root/.sonar/cache
13:23:24.382 INFO  Communicating with SonarQube Server 9.1.0.47736
13:23:24.927 INFO  Load global settings
13:23:25.019 INFO  Load global settings (done) | time=92ms
13:23:25.022 INFO  Server id: 9CFC3560-AZH_xxuuWTpPVl-GN5BT
13:23:25.041 INFO  User cache: /root/.sonar/cache
13:23:25.044 INFO  Load/download plugins
13:23:25.044 INFO  Load plugins index
13:23:25.198 INFO  Load plugins index (done) | time=154ms
13:23:26.812 INFO  Load/download plugins (done) | time=1768ms
13:23:28.395 INFO  Process project properties
13:23:28.395 INFO  Process project properties (done) | time=1ms
13:23:28.396 INFO  Execute project builders
13:23:28.397 INFO  Execute project builders (done) | time=1ms
13:23:28.400 INFO  Project key: hw_ci
13:23:28.400 INFO  Base dir: /opt/hw_ci_3
13:23:28.400 INFO  Working dir: /opt/hw_ci_3/.scannerwork
13:23:29.021 INFO  Load project settings for component key: 'hw_ci'
13:23:29.067 INFO  Load project settings for component key: 'hw_ci' (done) | time=46ms
13:23:29.127 INFO  Load quality profiles
13:23:29.234 INFO  Load quality profiles (done) | time=106ms
13:23:29.241 INFO  Load active rules
13:23:31.907 INFO  Load active rules (done) | time=2666ms
13:23:31.961 WARN  SCM provider autodetection failed. Please use "sonar.scm.provider" to define SCM of your project, or disable the SCM Sensor in the project settings.
13:23:32.067 INFO  Indexing files...
13:23:32.068 INFO  Project configuration:
13:23:32.068 INFO    Excluded sources for coverage: fail.py
13:23:32.472 INFO  271 files indexed
13:23:32.473 INFO  Quality profile for py: Sonar way
13:23:32.473 INFO  Quality profile for xml: Sonar way
13:23:32.473 INFO  ------------- Run sensors on module hw_ci
13:23:32.701 INFO  Load metrics repository
13:23:32.797 INFO  Load metrics repository (done) | time=97ms
13:23:34.733 INFO  Sensor Python Sensor [python]
13:23:34.740 WARN  Your code is analyzed as compatible with python 2 and 3 by default. This will prevent the detection of issues specific to python 2 or python 3. You can get a more precise analysis by setting a python version in your configuration via the parameter "sonar.python.version"
13:23:34.751 INFO  Starting global symbols computation
13:23:34.758 INFO  1 source file to be analyzed
13:23:34.772 INFO  Load project repositories
13:23:34.791 INFO  Load project repositories (done) | time=19ms
13:23:35.211 INFO  1/1 source file has been analyzed
13:23:35.212 INFO  Starting rules execution
13:23:35.214 INFO  1 source file to be analyzed
13:23:37.801 INFO  1/1 source file has been analyzed
13:23:37.801 INFO  Sensor Python Sensor [python] (done) | time=3068ms
13:23:37.802 INFO  Sensor Cobertura Sensor for Python coverage [python]
13:23:37.816 INFO  Sensor Cobertura Sensor for Python coverage [python] (done) | time=14ms
13:23:37.818 INFO  Sensor PythonXUnitSensor [python]
13:23:37.824 INFO  Sensor PythonXUnitSensor [python] (done) | time=6ms
13:23:37.824 INFO  Sensor CSS Rules [cssfamily]
13:23:37.826 INFO  No CSS, PHP, HTML or VueJS files are found in the project. CSS analysis is skipped.
13:23:37.826 INFO  Sensor CSS Rules [cssfamily] (done) | time=2ms
13:23:37.826 INFO  Sensor JaCoCo XML Report Importer [jacoco]
13:23:37.832 INFO  'sonar.coverage.jacoco.xmlReportPaths' is not defined. Using default locations: target/site/jacoco/jacoco.xml,target/site/jacoco-it/jacoco.xml,build/reports/jacoco/test/jacocoTestReport.xml
13:23:37.835 INFO  No report imported, no coverage information will be imported by JaCoCo XML Report Importer
13:23:37.835 INFO  Sensor JaCoCo XML Report Importer [jacoco] (done) | time=9ms
13:23:37.836 INFO  Sensor C# Project Type Information [csharp]
13:23:37.838 INFO  Sensor C# Project Type Information [csharp] (done) | time=2ms
13:23:37.839 INFO  Sensor C# Analysis Log [csharp]
13:23:38.382 INFO  Sensor C# Analysis Log [csharp] (done) | time=543ms
13:23:38.382 INFO  Sensor C# Properties [csharp]
13:23:38.382 INFO  Sensor C# Properties [csharp] (done) | time=0ms
13:23:38.382 INFO  Sensor JavaXmlSensor [java]
13:23:38.393 INFO  1 source file to be analyzed
13:23:39.054 INFO  1/1 source file has been analyzed
13:23:39.055 INFO  Sensor JavaXmlSensor [java] (done) | time=673ms
13:23:39.055 INFO  Sensor HTML [web]
13:23:39.059 INFO  Sensor HTML [web] (done) | time=3ms
13:23:39.059 INFO  Sensor XML Sensor [xml]
13:23:39.063 INFO  1 source file to be analyzed
13:23:39.203 INFO  1/1 source file has been analyzed
13:23:39.204 INFO  Sensor XML Sensor [xml] (done) | time=144ms
13:23:39.204 INFO  Sensor VB.NET Project Type Information [vbnet]
13:23:39.205 INFO  Sensor VB.NET Project Type Information [vbnet] (done) | time=1ms
13:23:39.205 INFO  Sensor VB.NET Analysis Log [vbnet]
13:23:39.218 INFO  Sensor VB.NET Analysis Log [vbnet] (done) | time=13ms
13:23:39.218 INFO  Sensor VB.NET Properties [vbnet]
13:23:39.219 INFO  Sensor VB.NET Properties [vbnet] (done) | time=0ms
13:23:39.221 INFO  ------------- Run sensors on project
13:23:39.236 INFO  Sensor Zero Coverage Sensor
13:23:39.265 INFO  Sensor Zero Coverage Sensor (done) | time=29ms
13:23:39.266 INFO  SCM Publisher No SCM system was detected. You can use the 'sonar.scm.provider' property to explicitly specify it.
13:23:39.275 INFO  CPD Executor Calculating CPD for 1 file
13:23:39.292 INFO  CPD Executor CPD calculation finished (done) | time=16ms
13:23:39.422 INFO  Analysis report generated in 83ms, dir size=104.7 kB
13:23:39.437 INFO  Analysis report compressed in 13ms, zip size=15.6 kB
13:23:39.489 INFO  Analysis report uploaded in 46ms
13:23:39.491 INFO  ANALYSIS SUCCESSFUL, you can browse http://89.169.164.127:9000/dashboard?id=hw_ci
13:23:39.497 INFO  Note that you will be able to access the updated dashboard once the server has processed the submitted analysis report
13:23:39.497 INFO  More about the report processing at http://89.169.164.127:9000/api/ce/task?id=AZIAJylKWTpPVl-GN-Ga
13:23:39.509 INFO  Analysis total time: 12.170 s
13:23:39.514 INFO  EXECUTION SUCCESS
13:23:39.515 INFO  Total time: 16.540s
```

</details>

7. Посмотрите результат в интерфейсе.

![изображение](https://github.com/stepynin-georgy/hw_ci_2/blob/main/img/Screenshot_25.png)

![изображение](https://github.com/stepynin-georgy/hw_ci_2/blob/main/img/Screenshot_27.png)

8. Исправьте ошибки, которые он выявил, включая warnings.
9. Запустите анализатор повторно — проверьте, что QG пройдены успешно.

![изображение](https://github.com/stepynin-georgy/hw_ci_2/blob/main/img/Screenshot_28.png)

10. Сделайте скриншот успешного прохождения анализа, приложите к решению ДЗ.

## Знакомство с Nexus

### Основная часть

1. В репозиторий `maven-public` загрузите артефакт с GAV-параметрами:

 *    groupId: netology;
 *    artifactId: java;
 *    version: 8_282;
 *    classifier: distrib;
 *    type: tar.gz.

![изображение](https://github.com/stepynin-georgy/hw_ci_2/blob/main/img/Screenshot_29.png)
   
2. В него же загрузите такой же артефакт, но с version: 8_102.

![изображение](https://github.com/stepynin-georgy/hw_ci_2/blob/main/img/Screenshot_30.png)

3. Проверьте, что все файлы загрузились успешно.
4. В ответе пришлите файл `maven-metadata.xml` для этого артефекта.

[maven-metadata.xml](https://github.com/stepynin-georgy/hw_ci_2/blob/main/maven-metadata.xml)

### Знакомство с Maven

### Подготовка к выполнению

1. Скачайте дистрибутив с [maven](https://maven.apache.org/download.cgi).
2. Разархивируйте, сделайте так, чтобы binary был доступен через вызов в shell (или поменяйте переменную PATH, или любой другой, удобный вам способ).
3. Удалите из `apache-maven-<version>/conf/settings.xml` упоминание о правиле, отвергающем HTTP- соединение — раздел mirrors —> id: my-repository-http-unblocker.
4. Проверьте `mvn --version`.

![изображение](https://github.com/stepynin-georgy/hw_ci_2/blob/main/img/Screenshot_31.png)

5. Заберите директорию [mvn](./mvn) с pom.

### Основная часть

1. Поменяйте в `pom.xml` блок с зависимостями под ваш артефакт из первого пункта задания для Nexus (java с версией 8_282).
2. Запустите команду `mvn package` в директории с `pom.xml`, ожидайте успешного окончания.

![изображение](https://github.com/stepynin-georgy/hw_ci_2/blob/main/img/Screenshot_32.png)

3. Проверьте директорию `~/.m2/repository/`, найдите ваш артефакт.
4. В ответе пришлите исправленный файл `pom.xml`.

[maven-metadata.xml](https://github.com/stepynin-georgy/hw_ci_2/blob/main/maven-metadata.xml)
---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.

---
