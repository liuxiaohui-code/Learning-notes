https://blog.csdn.net/cr7258/article/details/119583950?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522163247189216780264019211%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=163247189216780264019211&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-119583950.first_rank_v2_pc_rank_v29&utm_term=habor&spm=1018.2226.3001.4187

# harbor

```
https://blog.csdn.net/cr7258/article/details/119583950?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522163247189216780264019211%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=163247189216780264019211&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-119583950.first_rank_v2_pc_rank_v29&utm_term=habor&spm=1018.2226.3001.4187

helm repo add harbor-test-helm-repo --username=admin --password=Harbor12345 http://192.168.10.71:9000/harbor/
链接：https://www.jianshu.com/p/d5aabe1cd9e4


helm chart save CHART_PATH 192.168.10.71:9000/repo/REPOSITORY[:TAG] 

helm chart push 192.168.10.71:9000/repo/REPOSITORY[:TAG]




https://blog.csdn.net/mshxuyi/article/details/108217568


helm repo add harbor https://192.168.10.71/chartrepo/library

helm push  local --username admin --password Harbor12345
```

