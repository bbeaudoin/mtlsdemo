This example originally came from https://github.com/smallstep/certificates and was modified to use as an example of a client communicating with a server using mTLS (Mutual Transport Layer Security) authentication in OpenShift.

General overview for OpenShift installations:
1. Run the deploy script to create a project named 'test' and build the images
2. After installation, check the client and server logs for activity

If communication has been established properly

Example Logs:
```bash
[bbeaudoin@localhost mtlsdemo]$ oc logs dc/hello-mytls-client | head
2019-05-17 18:53:07,103 reloading certs ...
2019-05-17 18:53:07,104 reloading certs ...
2019-05-17 18:53:08,101 reloading certs ...
2019-05-17 18:53:08,101 reloading certs ...
2019-05-17 18:53:08,949 200 - OK - b'Hello World!\n'
2019-05-17 18:53:13,966 200 - OK - b'Hello World!\n'
2019-05-17 18:53:18,983 200 - OK - b'Hello World!\n'
2019-05-17 18:53:24,001 200 - OK - b'Hello World!\n'
2019-05-17 18:53:29,017 200 - OK - b'Hello World!\n'
2019-05-17 18:53:34,032 200 - OK - b'Hello World!\n'

[bbeaudoin@localhost mtlsdemo]$ oc logs dc/hello-mytls-server | head
[2019-05-17 18:52:45 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2019-05-17 18:52:45 +0000] [1] [INFO] Listening at: https://0.0.0.0:8443 (1)
[2019-05-17 18:52:45 +0000] [1] [INFO] Using worker: sync
[2019-05-17 18:52:45 +0000] [8] [INFO] Booting worker with pid: 8
[2019-05-17 18:52:45 +0000] [9] [INFO] Booting worker with pid: 9
10.130.2.222 - - [17/May/2019:18:53:08 +0000] "GET / HTTP/1.1" 200 13 "-" "-"
10.130.2.222 - - [17/May/2019:18:53:13 +0000] "GET / HTTP/1.1" 200 13 "-" "-"
10.130.2.222 - - [17/May/2019:18:53:18 +0000] "GET / HTTP/1.1" 200 13 "-" "-"
10.130.2.222 - - [17/May/2019:18:53:24 +0000] "GET / HTTP/1.1" 200 13 "-" "-"
10.130.2.222 - - [17/May/2019:18:53:29 +0000] "GET / HTTP/1.1" 200 13 "-" "-"
[bbeaudoin@localhost mtlsdemo]$ 
```

Without a published route, the service is only available from a node joined to the cluster's SDN. The certificates may be copied to a node and used with the `curl` command to test that the server has a trusted server certificate and a client certificate is required.

Example test of server and certificate enforcement:
```bash
[bbeaudoin@localhost mtlsdemo]$ scp -rp certs master1:
[bbeaudoin@localhost mtlsdemo]$ ssh master1

[cloud-user@master1 ~]$ curl https://hello-mytls-server.test.svc.cluster.local:8443 --cacert certs/root.crt 
curl: (35) NSS: client certificate not found (nickname not specified)

[cloud-user@master1 ~]$ curl https://hello-mytls-server.test.svc.cluster.local:8443 --cacert certs/root.crt --cert certs/client/site.crt --key certs/client/site.key
Hello World!
[cloud-user@master1 ~]$ 
```
