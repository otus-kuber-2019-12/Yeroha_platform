# Yeroha_platform
Yeroha Platform repository

Выполнил домашнее задание №1
Был собран docker image с простым приложением работающий по ссылке 
http://localhost:8000/homework.html
Так же написан мнифест кубера согласно домашнего задания. Локально все работает и приложение
доступно по адресу http://localhost:8000/index.html

Поды в namespace kube-system восстанавливаются, возможно потому что в манифесте указано restartPolicy: Always

Для запуска пода необходимо выполнить команду 
kubectl apply -f web-pod.yaml
