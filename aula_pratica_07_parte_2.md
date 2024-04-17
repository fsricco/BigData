# Aula 07

### Upload do Arquivo

Copie o arquivo `US_Accidents_March23.csv` para a pasta user_data:

```bash
docker exec -it spark-master /bin/bash
hadoop fs -mkdir /datasets/accidents
hadoop fs -put /user_data/US_Accidents_March23.csv  /datasets/accidents
```
