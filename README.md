# Задача 1: Работа с модулем Vault
Cоздали под с Vault через предложенный yaml.
<br>затем выяснили ip проверили ключ в переменных пода.

```shell
desema@control1:~$ kubectl get pod 14.2-netology-vault -o json | jq -c '.status.podIPs'
[{"ip":"10.233.110.37"}]
desema@control1:~$ kubectl exec --stdin --tty 14.2-netology-vault -- sh
/ # env | grep TOKEN
VAULT_DEV_ROOT_TOKEN_ID=aiphohTaa0eeHei
```
<br>Далее подняли pod на базе fedora, установили в него python и попробовали подключиться к нашему Vault, 
а так же записали и прочитали секрет, использовав токен и ip полученные выше:

```shell
>>> client = hvac.Client(
...     url='http://10.233.110.37:8200',
...     token='aiphohTaa0eeHei'
... )
>>>
>>> client.is_authenticated()
True
>>>
>>> client.secrets.kv.v2.create_or_update_secret(
...     path='hvac',
...     secret=dict(SecretKey='SecretValue!'),
... )
{'request_id': '4bdf1aeb-6ead-4839-4f6d-e935e15951f8', 'lease_id': '', 'renewable': False, 'lease_duration': 0, 'data': {'created_time': '2021-11-30T16:32:47.492895716Z', 'custom_metadata': None, 'deletion_time': '', 'destroyed': False, 'version': 2}, 'wrap_info': None, 'warnings': None, 'auth': None}
>>>
>>> client.secrets.kv.v2.read_secret_version(
...     path='hvac',
... )
{'request_id': '7b3e3993-566e-b42c-4b15-500ca6ca2e71', 'lease_id': '', 'renewable': False, 'lease_duration': 0, 'data': {'data': {'SecretKey': 'SecretValue!'}, 'metadata': {'created_time': '2021-11-30T16:32:47.492895716Z', 'custom_metadata': None, 'deletion_time': '', 'destroyed': False, 'version': 2}}, 'wrap_info': None, 'warnings': None, 'auth': None}
>>>
```

