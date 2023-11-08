# Install Orc8r
* Build imges and run containers

```bash
HOST [magma]$ cd orc8r/cloud/docker
HOST [magma/orc8r/cloud/docker]$ ./build.py --all
HOST [magma/orc8r/cloud/docker]$ ./run.py --metrics

Creating orc8r_alertmanager_1     ... done
Creating orc8r_maria_1            ... done
Creating elasticsearch            ... done
Creating orc8r_postgres_1         ... done
Creating orc8r_config-manager_1   ... done
Creating orc8r_test_1             ... done
Creating orc8r_prometheus-cache_1 ... done
Creating orc8r_prometheus_1       ... done
Creating orc8r_kibana_1           ... done
Creating fluentd                  ... done
Creating orc8r_proxy_1            ... done
Creating orc8r_controller_1       ... done
```
* Check if certs are genereated

```bash
HOST [magma/orc8r/cloud/docker]$ ls ../../../.cache/test_certs

admin_operator.key.pem  bootstrapper.key        controller.crt          rootCA.key
admin_operator.pem      certifier.key           controller.csr          rootCA.pem
admin_operator.pfx      certifier.pem           controller.key          rootCA.srl
```
* Change the ownership of two certs shown as follows:

```bash
sudo chown ${USER}:${USER} ../../../.cache/test_certs/admin_operator.key.pem
sudo chown ${USER}:${USER} ../../../.cache/test_certs/admin_operator.pfx 
```
* For chrome import the certificate manually, for firefox load certificate when prompted when you visit `Swagger-UI` else `NMS Dashboard` for the first time.
> Access `Swagger UI` at `https://localhost:9443/swagger/v1/ui`

## Bring up NMS Dashboard

```bash
HOST [magma]$ cd nms
HOST [magma/nms] $ COMPOSE_PROJECT_NAME=magmalte docker-compose build magmalte
HOST [magma/nms] $ docker-compose up -d
HOST [magma/nms] $ ./scripts/dev_setup.sh
```
* Access `NMS Dashboard` at `https://magma-test.localhost/`
> Login ID `admin@magma.test`
> Password `password1234`

### Restart orc8r

```bash
sudo docker container stop $(docker container ls -aq)
sudo docker container rm $(docker container ls -aq)
```
> go to ${MAGMA_ROOT}/orc8r/cloud/docker/

```bash
./run.py --metrics
```
> got to ${MAGMA_ROOT}/nms/

```bash
docker-compose up -d
./scripts/dev_setup.sh
```
