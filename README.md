# Домашнее задание к занятию "GitLab" - Зеленкин С.С.
 
### Задание 1

1. Разверните GitLab локально, используя Vagrantfile и инструкцию, описанные в [этом репозитории](https://github.com/netology-code/sdvps-materials/tree/main/gitlab).   
2. Создайте новый проект и пустой репозиторий в нём.
3. Зарегистрируйте gitlab-runner для этого проекта и запустите его в режиме Docker. Раннер можно регистрировать и запускать на той же виртуальной машине, на которой запущен GitLab.
В качестве ответа в репозиторий шаблона с решением добавьте скриншоты с настройками раннера в проекте.

### Решение 1 

1. GitLab развернул на ВМ в Яндекс.Клауд. Версия 16.
2. Пустой проект не заскринил. Прикладываю скрин коммитов
![Скриншот-1](https://github.com/zelenkins/gitlab_hw/blob/main/img/1.png)
3. 
![Скриншот-2](https://github.com/zelenkins/gitlab_hw/blob/main/img/r1.png)
![Скриншот-3](https://github.com/zelenkins/gitlab_hw/blob/main/img/r2.png)

---

### Задание 2

1. Запушьте [репозиторий](https://github.com/netology-code/sdvps-materials/tree/main/gitlab) на GitLab, изменив origin. Это изучалось на занятии по Git.
2. Создайте .gitlab-ci.yml, описав в нём все необходимые, на ваш взгляд, этапы.

В качестве ответа в шаблон с решением добавьте: 
   
 * файл gitlab-ci.yml для своего проекта или вставьте код в соответствующее поле в шаблоне; 
 * скриншоты с успешно собранными сборками.

 ### Решение 2

1. Пуш первого коммита
![Скриншот-4](https://github.com/zelenkins/gitlab_hw/blob/main/img/initial_commit.png)
2. Сначала создал .gitlab-ci.yml для docker
```yaml
stages:
  - build
  - test

build_job:
  stage: build
  tags:
    - docker
  script:
    - echo "Building the project..."

test_job:
  stage: test
  tags:
    - docker
  script:
    - echo "Running tests..."
```
Но пайплайн отрабатывал с ошибкой. Как я понял позднее, текущая версиея GitLab Runner 10.5.0 не работала с современными Docker-образами. 
Я решил просто сделать yml, чтобы он отработал - разобраться в теме. Реализовал на shell: 

```yaml
stages:
  - build

build_job:
  stage: build
  tags:
    - shell
  script:
    - echo "Building 3..."
```
Результаты:
![Скриншот-5](https://github.com/zelenkins/gitlab_hw/blob/main/img/p1.png)
![Скриншот-6](https://github.com/zelenkins/gitlab_hw/blob/main/img/p2.png)

---

### Задание 3*

Измените CI так, чтобы:

 - этап сборки запускался сразу, не дожидаясь результатов тестов;
 - тесты запускались только при изменении файлов с расширением *.go.

### Решение 3*

Пример файла:
1. Указываем needs: []
2. И правило на изменения "**/*.go" - любая вложенность каталогов и расширение файла .go

```yaml
stages:
  - build
  - test

build_job:
  stage: build
  tags:
    - docker
  script:
    - echo "Building the project..."

test_job:
  stage: test
  needs: []
  tags:
    - docker
  script:
    - echo "Running tests..."
  rules:
    - changes:
        - "**/*.go"
```