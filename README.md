# devops-netology
hello world

.gitignore в директории terraform не даст добавить  в репозиторий следующие типы файлов:
локальные terraform директории, файлы с расширением .tfstate, crash логи, файлы с расширением .tfvars, т.к. они могут содержать конфиденциальные данные, файлы переопределенния override.tf и конфигурационные файлы командной строки.
#